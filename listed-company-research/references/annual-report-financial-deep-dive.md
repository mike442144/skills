# Annual Report Financial Appendix Deep-Dive

Worked example: 中材国际（600970）2025年年报 应收账款风险分析

## Extraction Workflow

### Step 1: Locate AR aging tables in PDF

```python
import fitz

doc = fitz.open(pdf_path)
# Search for aging + AR pages (skip TOC)
found = []
for i in range(len(doc)):
    text = doc[i].get_text()
    if '账龄' in text and '应收账款' in text:
        found.append(i)
# Found pages typically: [126, 155, 157] (合并) and [280, 282, 283] (母公司)
```

**Key pages to extract:**
- Page with aging table: `1年以内/1-2年/2-3年/3-4年/4-5年/5年以上` with 期末/期初余额
- Page with provision-by-aging table: `坏账准备` with `计提比例(%)`
- Page with top-5 obligors: `前五名` with 应收账款+合同资产 combined
- Page with ECL/provision movement: `本期变动金额` showing 期初→计提→转销→期末

### Step 2: Key data points extracted (中材国际 2025)

**AR Aging (合并, 亿元):**

| 账龄 | 2025年末 | 占比 | 2024年末 | 占比 | 变动 |
|------|---------|------|---------|------|------|
| 1年以内 | 79.57 | 59% | 62.30 | 56% | +17.27 |
| 1-2年 | 20.83 | 15% | 25.59 | 23% | -4.76 |
| 2-3年 | 14.10 | 10% | 10.14 | 9% | +3.96 |
| 3-4年 | 9.28 | 7% | 5.30 | 5% | +3.98 |
| 4-5年 | 3.95 | 3% | 2.29 | 2% | +1.66 |
| 5年以上 | 7.65 | 6% | 6.09 | 5% | +1.56 |

**Critical insight:** 1年以内占比上升 (56%→59%) creates false sense of improvement. But 3年以上合计 grew 53% (13.7→20.9亿), showing old project receivables accumulating.

**Provision rates:**

| 账龄 | 余额(亿) | 坏账(亿) | 计提率 |
|------|---------|---------|--------|
| 1年以内 | 79.57 | 1.91 | 2.4% |
| 1-2年 | 20.83 | 1.60 | 7.7% |
| 2-3年 | 13.73 | 2.23 | 16.3% |
| 3-4年 | 9.14 | 1.88 | 20.6% |
| 4-5年 | 3.89 | 1.48 | 38.1% |
| 5年以上 | 7.17 | 7.17 | 100% |

### Step 3: Derived metrics to calculate

**Aging migration impact:** When 3-4年段 (9.28亿, 20.6% provision) ages into 4-5年段 (38.1% provision), the incremental provision needed = 9.28 × (38.1% - 20.6%) ≈ 1.6亿. Similarly 4-5年 → 5年以上 adds ~2.5亿. Total mechanical migration pressure: ~4亿 next year.

**"两金" (AR + Contract Assets) as % of revenue:**
- 中材国际 2025: (118.2 + 118.9) / 496.0 = 48%
- 宇通客车 2025: 53.8 / 414.3 = 13%
- Ratio difference (3.7x) reflects business model: EPC (project-based, long collection cycle) vs manufacturing (cash on delivery)

**Credit impairment loss trend:** Pull multi-year from Wind (`信用减值损失`). Key signal: 中材 2025 credit impairment jumped from 1.23亿 to 5.76亿 (+369%), total impairment (信用+资产) = 10.06亿 = 35% of net profit.

### Step 4: Top-5 obligor analysis

Extract from PDF page containing `前五名`. Note: this table combines 应收账款 + 合同资产.

**Red flags to look for:**
- Single client >10% of total AR+contract assets
- Provision rate <1% for large overseas clients (e.g., FOX Company: 21.17亿, only 0.3% provisioned)
- Clients in countries with currency controls or political risk (尼日利亚, 埃及)

### Step 5: Cross-reference with cash flow

Always connect balance sheet findings to income statement and cash flow:
- 经营现金流 declining while AR growing = collection deterioration
- Management's own explanation in 业绩说明会公告 (retrievable via `get_company_announcements`)
- Example: 中材 management explicitly said "下游业主整体支付能力与结算节奏放缓" — confirms the AR risk is real and management is aware

## Pitfalls

1. **Contract assets (合同资产) are equally important** — they represent "revenue recognized but not yet billable". For EPC companies, contract assets can be as large as AR. Always analyze both together as "两金".
2. **Provision rates vary by company** — don't apply a uniform 5% benchmark. Check what the company's own aging-based rates are, then assess if those rates are adequate given the client base quality.
3. **Parent vs consolidated** — the annual report has aging tables for both 合并 and 母公司. Always use 合并 (consolidated) for analysis.
4. **Wind's "坏账准备" and "信用减值损失" are different things** — 坏账准备 is a balance sheet item (cumulative provision), 信用减值损失 is an income statement item (annual provision expense). Both are needed.
