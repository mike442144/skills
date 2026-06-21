# cninfo Annual Report Download & MD&A Extraction

## Downloading Annual Reports

### Tool: `~/Projects/tinyant/cninfo/index.js`

**CRITICAL**: The `--annual` flag is REQUIRED for annual reports. Without it, the tool defaults to recent announcements mode (last month only).

```bash
# Correct — downloads annual report PDFs
node ~/Projects/tinyant/cninfo/index.js --codes 600066 --annual --year 2015-2024

# WRONG — downloads only recent announcements (ignores --year)
node ~/Projects/tinyant/cninfo/index.js --codes 600066 --year 2015-2024
```

Output goes to `~/Projects/tinyant/cninfo/data/<stock_code>/` with naming pattern `<YEAR>_<公司简称>_年度报告.pdf`.

The tool automatically filters out summaries (摘要), English versions, and corrected reports — it downloads the main report only.

## MD&A Extraction from PDF

### Tool: PyMuPDF (fitz)

```python
import fitz
doc = fitz.open(pdf_path)
full_text = "\n".join([page.get_text() for page in doc])
```

### Pitfall 1: TOC vs Content

Chinese annual reports have the MD&A section title in the TABLE OF CONTENTS (typically pages 2-4) AND in the actual content (pages 7-10+). A naive regex will match the TOC entry first, extracting only the table of contents text (a few lines with dots/ellipses).

**Detection**: If extracted text is < 1000 chars, you almost certainly hit the TOC.

### Pitfall 2: Section Number Varies by Year

- 2015: MD&A is in **第四节** (Section 4)
- 2016-2025: MD&A is in **第三节** (Section 3)

Only 2015 used 第四节. The change happened after 2015, not after 2020. Do NOT hardcode the section number. Use a regex that matches both:
```python
pattern = r'第[三四]节\s*管理层讨论与分析'
```

**Validation**: This extraction function has been tested across 2015-2025 (11 consecutive years) for 宇通客车 (600066.SH), producing 15,000-65,000 chars per year.

### Pitfall 3: Multiple Occurrences

The section header appears at least twice (TOC + content). Solution: find ALL occurrences, pick the LAST one.

```python
matches = list(re.finditer(r'第[三四]节\s*管理层讨论与分析', full_text))
start_pos = matches[-1].start()  # Last occurrence = actual content
```

### End Marker Detection

The MD&A section ends at the next major section. Common end markers:
```python
end_patterns = [
    r'\n第[四五]节\s+.*(公司治理|环境与社会责任|重要事项)',
    r'\n重要事项',
    r'\n公司治理',
]
```
Search for these AFTER `start_pos + 500` to avoid matching the TOC's own listing of subsequent sections.

### Complete Extraction Function

```python
import fitz
import re

def extract_mda(pdf_path):
    doc = fitz.open(pdf_path)
    full_text = "\n".join([page.get_text() for page in doc])
    
    # Find ALL occurrences of section header
    pattern = r'第[三四]节\s*管理层讨论与分析'
    matches = list(re.finditer(pattern, full_text))
    if not matches:
        # Fallback: search for content marker directly
        match = re.search(r'一、经营情况讨论与分析', full_text)
        if match:
            start_pos = match.start()
        else:
            return None
    else:
        start_pos = matches[-1].start()  # Last = content, not TOC
    
    # Find end
    end_pos = len(full_text)
    for pat in [r'\n第[四五]节\s+.*(公司治理|环境与社会责任|重要事项)',
                r'\n重要事项', r'\n公司治理']:
        m = re.search(pat, full_text[start_pos + 500:])
        if m:
            candidate = start_pos + 500 + m.start()
            if candidate < end_pos:
                end_pos = candidate
    
    return full_text[start_pos:end_pos].strip()
```

### Typical Output Sizes

A properly extracted MD&A section from a Chinese annual report is typically 15,000-65,000 characters. Anything under 1,000 chars indicates a failed extraction (TOC match).

## Keyword Filtering for Thematic Analysis

After extracting MD&A from multiple years, filter for thematic content:

```python
keywords = ['海外', '出口', '国际', '境外', '全球化', '一带一路']

def extract_themed_paragraphs(text, keywords):
    paragraphs = re.split(r'\n\s*\n', text)
    relevant = []
    for p in paragraphs:
        if any(kw in p for kw in keywords):
            clean = re.sub(r'\s+', ' ', p).strip()
            if len(clean) > 30:
                relevant.append(clean)
    return relevant
```

This produces year-by-year themed excerpts suitable for timeline/strategy analysis.
