# Pharma Annual Report: Product-Line Revenue Data Hierarchy

## Problem
The standard "主营构成分析" (main business composition) table in pharma annual reports is often too coarse for product-level analysis. It typically shows only 2-3 categories:
- 产品维度: "药品及其他" + "医疗器械" (+ "其他业务")
- 行业维度: "医药制造业" + "医药批发及相关业务"
- 地区维度: "中国大陆" + "国外"

This level of detail is insufficient for understanding which therapeutic areas or product lines drive growth and margins.

## Solution: Three-Level Data Hierarchy

### Level 1: 核心产品毛利率表 (Core Product Gross Margin Table)
**Location:** MD&A section (经营情况讨论与分析), typically under "核心竞争力分析" or a dedicated "核心产品毛利率" subsection.

**Structure (pharma-specific, organized by 治疗领域):**

| 治疗领域 | 营业收入(万元) | 营业成本(万元) | 毛利率(%) | 收入同比(%) | 成本同比(%) | 毛利率同比(pct) | 同行业同领域产品毛利率 |
|---------|---------------|---------------|----------|------------|------------|----------------|---------------------|
| 神经系统用药 | 907,570.24 | 127,247.61 | 85.98 | +6.28 | +1.46 | +0.67 | 85.03% |
| 甾体激素类药物 | 94,194.88 | 47,205.94 | 49.88 | +30.60 | +22.96 | +3.11 | 64.49% |
| 维吾尔药 | 112,316.60 | 23,677.09 | 78.92 | +12.15 | +20.28 | -1.43 | 64.16% |

**Key features:**
- Breaks down by therapeutic area (治疗领域), not just product category
- Includes peer comparison (同行业同领域产品毛利率) with source attribution
- Shows cost structure and margin trends, not just revenue
- Unit is 万元 (10,000 RMB), not 元

**Peer comparison sources (typical):**
- 神经系统用药 → 恩华药业 "麻醉类、精神类、神经类" 综合毛利率
- 甾体激素类 → 仙琚制药 "妇科及计生用药" 毛利率
- 维吾尔药 → 和田维药 毛利率

### Level 2: 子公司经营数据 (Subsidiary Operating Data)
**Location:** MD&A section, under "主要控股参股公司分析" or "主要经营子公司情况".

**Structure:**

| 子公司 | 持股比例 | 营业收入(万元) | 净利润(万元) | 收入同比 | 利润同比 |
|--------|---------|---------------|-------------|---------|---------|
| 宜昌人福 | 80% | 870,177.97 | 270,319.73 | +7.97% | +11.30% |
| 葛店人福 | 81.07% | 132,172.75 | 24,178.07 | +10.66% | +29.07% |
| Epic Pharma | 100% | 135,276.65 | 13,956.59 | +27.83% | +21.16% |
| 新疆维药 | - | ~112,316 | ~23,677 | +12.15% | - |

**Key features:**
- Each subsidiary typically maps to a product line/therapeutic area
- Provides both revenue AND net profit (unlike 核心产品毛利率表 which only shows gross margin)
- Includes ownership percentage (important for minority interest calculations)
- Useful for cross-checking against Level 1 data

### Level 3: 管理层定性描述 (Management Qualitative Commentary)
**Location:** MD&A section, business review paragraphs.

**Typical disclosures:**
- Specific product sales figures mentioned in text (e.g., "注射用苯磺酸瑞马唑仑、盐酸羟考酮缓释片等近年来新上市产品实现较快增长")
- Regional breakdowns (e.g., "美国仿制药业务实现销售收入约20亿元", "非洲市场销售收入约1.44亿元")
- New product pipeline and registration progress
- Market share claims (e.g., "国内最大的维吾尔药生产企业")

## Extraction Methods

### Method 1: cninfo PDF + pymupdf find_tables() (PREFERRED for structured tables)

**When to use:** Extracting complete structured tables (核心产品毛利率表, 子公司经营数据, 主营构成表) from annual reports. More reliable than RAG — guarantees 100% accuracy from the original PDF.

**Workflow:**

```bash
# Step 1: Download annual report PDF via cninfo downloader
cd ~/Projects/tinyant/cninfo
node index.js --codes 600079 --year 2020-2025
# PDFs saved to: data/<stock_code>/<year>_<公司简称>_年度报告.pdf
```

```python
# Step 2: Extract structured tables with pymupdf
import pymupdf
import json

doc = pymupdf.open("data/600079/2024_ST人福_年度报告.pdf")

for page_num in range(len(doc)):
    page = doc[page_num]
    text = page.get_text()
    # Search for the target table by keyword combination
    if "毛利率" in text and ("治疗领域" in text or "主要药" in text):
        tabs = page.find_tables()
        if tabs and tabs.tables:
            for tab in tabs.tables:
                data = tab.extract()
                if data and len(data) > 1:
                    header = data[0]
                    if "治疗领域" in str(header) or "营业收入" in str(header):
                        print(f"Page: {page_num}")
                        print(json.dumps({"header": header, "rows": data[1:]}, ensure_ascii=False, indent=2))
                        break
doc.close()
```

**Key patterns:**
- `find_tables()` returns structured table data as list-of-lists — no OCR or text parsing needed
- Search by keyword combination (e.g., "毛利率" + "治疗领域") to locate the right page
- Validate table by checking header row contains expected column names
- Can batch-process multiple years in one script

**Fallback when `find_tables()` doesn't detect the table:**
Some older annual reports (e.g., 2019年报) have tables that `find_tables()` fails to detect. Use text-based fallback:

```python
# Fallback: text search when find_tables() returns nothing
for page_num in range(len(doc)):
    text = doc[page_num].get_text()
    if "按治疗领域划分的公司主营业务基本情况" in text:
        # Table title found — extract surrounding text
        idx = text.find("单位：万元")
        if idx > 0:
            table_text = text[idx:idx+1500]
            # Parse manually or use regex
            print(f"Page {page_num}: {table_text}")
```

Common table title patterns to search for:
- "按治疗领域划分的公司主营业务基本情况"
- "按治疗领域或主要药（产）品等分类划分的经营数据情况"
- "核心产品毛利率表"

**Advantages over RAG:**
- 100% data accuracy — reads directly from the original PDF
- Complete table extraction — no risk of row truncation
- Batch-friendly — process multiple years in one script
- No query keyword engineering needed

**Pitfall — LLM-based extraction (e.g., report-parser) is NOT recommended for structured tables:**
For tables like 核心产品毛利率表, pymupdf `find_tables()` is faster, more accurate, and cheaper than LLM-based extraction. LLM extraction adds latency (seconds per call), cost (token fees), and hallucination risk. Use LLM only for narrative/qualitative extraction (e.g., management commentary, risk factors), not for structured numeric tables.

### Method 2: ifind-repilot-announcement-search RAG (for quick lookups)

**When to use:** Quick single-number lookups, verifying a specific data point, or when you don't need the full table. Faster than downloading PDFs.

### Query patterns for 核心产品毛利率表:
```
# Get the full table
python3 scripts/fetch_data.py --query "人福医药2024年治疗领域营业收入营业成本毛利率神经系统用药甾体激素维吾尔药" --page_size 5

# Get specific product line
python3 scripts/fetch_data.py --query "人福医药甾体激素类药物营业收入营业成本毛利率2024" --page_size 3

# Get with peer comparison
python3 scripts/fetch_data.py --query "人福医药核心产品毛利率同行业同领域产品毛利率情况" --page_size 3
```

### Key search terms that trigger the right table:
- "治疗领域 营业收入 营业成本 毛利率" — matches the exact table headers
- "同行业同领域产品毛利率情况" — unique to this table, high precision
- "核心产品毛利率" — section header

### Pitfalls:
- **RAG may return fragments, not complete tables.** Query multiple times with different therapeutic area names to ensure completeness. For complete tables, use Method 1 (PDF extraction).
- The table unit is 万元, not 元 or 亿元. Always check.
- Not all pharma companies disclose this table. It's more common in companies with multiple distinct therapeutic areas (e.g., 人福医药, 恒瑞医药, 恩华药业). Single-product companies may not have it.
- The peer comparison column cites specific competitors' annual reports — useful for cross-company validation.
- **cninfo downloader notes:** Auto-filters summaries/English versions/corrections. Supports resume (skips existing PDFs). 10-second request interval to avoid rate limiting. Output in `data/<stock_code>/` directory.

## Example: 人福医药 (600079) 2024 Annual Report

**核心产品毛利率表 (2024):**

| 治疗领域 | 营业收入 | 营业成本 | 毛利率 | 收入同比 | 成本同比 | 毛利率变动 |
|---------|----------|----------|--------|----------|----------|-----------|
| 神经系统用药 | 90.76亿 | 12.72亿 | 85.98% | +6.28% | +1.46% | +0.67pct |
| 甾体激素类药物 | 9.42亿 | 4.72亿 | 49.88% | +30.60% | +22.96% | +3.11pct |
| 维吾尔药 | 11.23亿 | 2.37亿 | 78.92% | +12.15% | +20.28% | -1.43pct |

**Cross-check with subsidiary data:**
- 宜昌人福 (神经系统用药主体): 营收87.02亿, 净利27.03亿
- 葛店人福 (甾体激素主体): 营收13.22亿, 净利2.42亿
- 新疆维药 (维吾尔药主体): 营收~11.23亿 (matches Level 1)
- Epic Pharma (美国仿制药): 营收13.53亿, 净利1.40亿

**Note:** 美国仿制药 and other lines may not appear in the 核心产品毛利率表 but are visible in subsidiary data and management commentary.

## Multi-Year Analysis: Treatment Area Name Mapping

When compiling multi-year data, treatment area names may change across annual reports. Use the latest name as the canonical label and annotate historical names.

### 人福医药 (600079) Name Mapping (2015-2025):

| Canonical Name (2025) | Historical Names | Years |
|----------------------|------------------|-------|
| 中枢神经 | 中枢神经用药 (2015-2016), 中枢神经系统用药 (2016), 神经系统用药 (2017-2024) | 2015-present |
| 甾体激素及两性健康 | 生育调节药 (2015-2019), 甾体激素类药物 (2020-2024) | 2015-present |
| 维吾尔药 | 维吾尔民族药 (2015-2023) | 2015-present |

### Data Availability Notes:

- **Table first appears:** 2015 annual report (not in 2014 or earlier)
- **Dropped categories:**
  - 生物制品 (biological products): Present 2015-2017, dropped from 2018 onwards. The subsidiary 中原瑞德 was sold to CSL Behring in July 2017 for ~$352M (80% stake).
  - 诊断试剂 (diagnostic reagents): Present 2015-2016, dropped from 2017 onwards. This was a distribution business (罗氏体外诊断试剂 regional distributor via 北京巴瑞), not manufacturing.
- **Peer comparison sources changed over time:**
  - 维吾尔药: 银朵兰 (831637.OC) 2015-2018年报 → 和田维药 (837527.NQ) 2019+ (company stated it could not obtain comparable data after 2018)
  - 神经系统用药: Consistently compared to 恩华药业 "麻醉类、精神类、神经类" products
  - 甾体激素类: Consistently compared to 仙琚制药 "妇科及计生用药"
