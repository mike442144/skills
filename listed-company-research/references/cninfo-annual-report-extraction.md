# cninfo Annual Report Download & MD&A Extraction

## Downloading Annual Reports

### Tool: `~/Projects/tinyant/cninfo/index.js`

```bash
cd ~/Projects/tinyant/cninfo && node index.js --codes 600066 --annual --year 2015-2024
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

### Pitfall 2: Section Number AND Name Both Vary

The section number varies by year, AND the section name itself also varies across companies:

| Year range | Section number | Section name variant |
|-----------|---------------|---------------------|
| 2015 | 第四节 | 经营情况讨论与分析 |
| 2016-2019 | 第三节 | 管理层讨论与分析 (typical) |
| 2020 | 第四节 (some companies) | 经营情况讨论与分析 |
| 2021-2025 | 第三节 | 管理层讨论与分析 (typical) |

**Critical**: Do NOT assume a fixed section number OR name. Some companies (e.g., 天山股份 2020) use "第四节 经营情况讨论与分析" even in post-2015 reports. The regex must match BOTH name variants:
```python
pattern = r'第[三四]节\s*(?:管理层讨论与分析|经营情况讨论与分析)'
```

**Validation**: Tested across 2015-2025 for 宇通客车 (600066.SH) and 天山股份 (000877.SZ), producing 15,000-65,000 chars per year. The 天山股份 2020 report confirmed the need for the broader pattern — the original narrow pattern (`管理层讨论与分析` only) failed to match because that year used "经营情况讨论与分析".

### Pitfall 3: Multiple Occurrences — Don't Assume "Last = Content"

The section header appears multiple times: the TOC entry, the actual section start, and often **cross-references** in later sections (e.g., "具体详见'第三节 管理层讨论与分析'之……" in 重要事项/公司治理 chapters).

**Do NOT blindly pick the last match.** In some reports (e.g., 伟星新材 2021/2022/2024) the last occurrence is a cross-reference, not the content — extracting from it yields garbage.

**General rule**: among all occurrences, pick the one that is immediately followed by actual section content — i.e., the text right after it starts with a subsection heading ("一、报告期内……" style). TOC entries are followed by dotted leaders and page numbers; cross-references sit mid-sentence inside other chapters. If in doubt, extract from each candidate and keep the one that produces a sane-length result (see Typical Output Sizes below).

```python
def find_content_start(matches, full_text):
    # The real section start is followed by a subsection heading like "一、…"
    for m in matches:
        window = re.sub(r'\s', '', full_text[m.end():m.end() + 200])
        if re.match(r'[一二三四五六七八九十]+、', window):
            return m.start()
    return matches[-1].start()  # fallback; verify output length afterwards
```

(The heading check is a heuristic, not a hard rule — adapt if a report formats its subsections differently, and always sanity-check the extracted length.)

### End Marker Detection

The MD&A section ends at the next major section. Common end markers:
```python
end_patterns = [
    r'\n第[四五]节\s*.*(公司治理|环境与社会责任|重要事项)',
    r'\n重要事项',
    r'\n公司治理',
]
```
Use `\s*` (not `\s+`) — some reports print "第四节公司治理" with no space. Search for these AFTER `start_pos + 500` to avoid matching the TOC's own listing of subsequent sections. If the result is implausibly long (the whole report), the end marker missed — inspect the text around the next section boundary and adapt the pattern for that report's layout.

### Complete Extraction Function

```python
import fitz
import re

def extract_mda(pdf_path):
    doc = fitz.open(pdf_path)
    full_text = "\n".join([page.get_text() for page in doc])
    
    # Find ALL occurrences of section header
    pattern = r'第[三四]节\s*(?:管理层讨论与分析|经营情况讨论与分析)'
    matches = list(re.finditer(pattern, full_text))
    if not matches:
        # Fallback: search for content marker directly (either variant)
        match = re.search(r'[一二]、(?:经营情况|管理层)讨论与分析', full_text)
        if match:
            start_pos = match.start()
        else:
            return None
    else:
        start_pos = find_content_start(matches, full_text)  # see Pitfall 3
    
    # Find end
    end_pos = len(full_text)
    for pat in [r'\n第[四五]节\s*.*(公司治理|环境与社会责任|重要事项)',
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
