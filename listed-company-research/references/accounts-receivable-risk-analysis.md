# Accounts Receivable Risk Analysis for Non-Financial Companies

When a company has large accounts receivable (应收账款) or contract assets (合同资产) relative to revenue — especially in project-based industries (EPC, construction, defense, large equipment) — a dedicated AR risk deep-dive is essential. This is often the single most important risk factor for such companies.

## When to Load This Reference

- AR + contract assets > 30% of revenue
- User mentions AR as a concern or reason for selling
- Credit impairment losses (信用减值损失) are growing year-over-year
- Company operates in a project-bidding / EPC business model with slow collections
- Industry downturn is pressuring customers' (甲方) payment ability

## Data Collection Checklist

All data below is extractable from the annual report PDF financial statement notes (附注), typically in pages 120-170 of a ~300-page A-share annual report.

### 1. Multi-Year "Two Gold" (两金) Trend

Query via wind-mcp first for the headline numbers:
```bash
node scripts/cli.mjs call stock_data get_stock_fundamentals '{"question":"600970.SH2015到2025年应收账款存货合同资产总资产营业收入"}'
```

Then calculate:
- **AR + contract assets as % of revenue** — track over 5-10+ years
- **AR + contract assets as % of total assets** — track balance sheet bloat
- The ratio trend (rising ratio = deteriorating cash conversion)

### 2. Aging Table (账龄分析) — From Annual Report PDF

This is NOT available via wind-mcp. Must extract from PDF.

**Extraction pattern:**
```python
import fitz
doc = fitz.open("2025_中材国际_年度报告.pdf")
# Search for pages containing '账龄' and '应收账款'
for page_num in range(len(doc)):
    text = doc[page_num].get_text()
    if '账龄' in text and '1 年以内' in text and '应收账款' in text:
        # This page has the aging table
```

**Key data to extract from the aging table:**
| 账龄 | 账面余额 | 占比 | 坏账准备 | 计提比例 |
|------|---------|------|---------|---------|
| 1年以内 | XX亿 | XX% | XX亿 | X.X% |
| 1-2年 | ... | | | |
| 2-3年 | ... | | | |
| 3-4年 | ... | | | |
| 4-5年 | ... | | | |
| 5年以上 | ... | | | 100% |

**Year-over-year comparison:** Always extract the same table from the prior year's annual report to show aging migration. Key deterioration signals:
- **3年以上 old AR growing faster** than total AR (long-uncollected items accumulating)
- **Aging migration risk:** Each year, receivables naturally shift to the next-higher aging bucket. When 3-4年 AR (20% provision) migrates to 4-5年 (38% provision), the company must mechanically top up the provision — even without any new deterioration. Calculate: `aging_bucket_balance × (next_rate - current_rate)` = forced additional provision.

### 3. Impairment Loss Trend (减值损失)

```bash
node scripts/cli.mjs call stock_data get_stock_fundamentals '{"question":"600970.SH2015到2025年应收账款坏账准备信用减值损失资产减值损失"}'
```

**Key metrics:**
- 信用减值损失 (credit impairment) — reflects expected credit losses on receivables
- 资产减值损失 (asset impairment) — reflects inventory write-downs, contract asset impairments
- **Combined as % of net profit** — when this exceeds 20%, it's a major profit drag
- **YoY growth rate** — a sudden spike (e.g., +369% in one year) signals either deteriorating asset quality or management "washing the books" (洗大澡)

### 4. Top-5 Counterparty Analysis

Extract from annual report notes — usually labeled "按欠款方归集的期末余额前五名". Key data:
- Customer name (may be anonymized for overseas clients)
- AR balance + contract asset balance per customer
- % of total receivables
- Bad debt provision per customer
- **Provision rate per customer** — if a top-5 client has <1% provision rate, flag it as potentially under-provisioned

**Assessment:** Single-customer concentration > 9% warrants attention. Overseas clients with low provision rates are the highest risk — legal recovery mechanisms abroad are weaker than domestic.

### 5. Contract Asset (合同资产) — The Hidden AR

Contract assets are often as large as AR for EPC/construction companies, but receive less attention. They represent revenue recognized under percentage-of-completion but not yet billable. Key points:
- Contract assets have **lower recovery priority** than billed AR — if a project is cancelled or disputed, contract assets are harder to recover
- Track the contract asset impairment provision rate separately — it's often lower than AR provision rates (meaning less conservatively provided)
- Year-over-year provision additions on contract assets signal management's growing caution

## Analysis Framework

### The "Slow Bleed" Assessment

For project/EPC companies, AR risk is typically a **slow deterioration**, not a sudden blowup. The pattern is:
1. Industry downturn weakens 甲方 (customer) payment ability
2. AR aging gradually worsens (more 3+ year old items)
3. Provision requirements mechanically increase (aging migration)
4. Credit impairment losses spike
5. Operating cash flow deteriorates (cash gap widens vs accrual profit)
6. Eventually: either industry recovers (risk normalizes) or impairment forces a profit collapse

**Key question to answer for the user:** "Is the AR risk a cyclical issue (will recover when industry picks up) or a structural deterioration (the company's business model inherently generates more paper revenue than cash)?"

### Comparison with Manufacturing Peers

Always compare AR metrics against a manufacturing/product company to highlight the structural difference:

| Metric | EPC/Project company | Manufacturing company |
|--------|--------------------|--------------------|
| AR / Revenue | 30-50%+ | 10-15% |
| Cash conversion cycle | 80-160 days | 30-50 days |
| Contract assets | Often > AR | Minimal |
| Impairment as % of net profit | 15-35%+ | 2-5% |

This comparison helps the user understand why project companies trade at lower multiples even with similar headline growth.

## Report Integration

Present the AR analysis as a dedicated appendix (附录C or equivalent), structured as:
1. **"Two gold" (两金) expansion history** — 10-year table showing AR + contract assets vs revenue
2. **Aging structure analysis** — latest year aging table with YoY comparison + deterioration signals
3. **Provision adequacy assessment** — whether current provision rates are sufficient, and forced migration impact
4. **Impairment loss trend** — the "profit erosion" table showing impairments as % of net profit
5. **Top-5 counterparty risk** — concentration and individual provision rate flags
6. **Conclusion** — explicit verdict: "The user's concern about AR risk is [justified/partially justified/manageable] because [specific reasons]"

## Session Case Study: 中材国际 (600970) — 2026-06-19

User sold the stock specifically because of AR concerns. Deep-dive findings validated the decision:
- 两金 expanded from 37亿 (2015) to 237亿 (2025), 6.4x growth vs 2.2x revenue growth
- 3-year+ old AR grew 53% YoY (13.7→20.9亿) — aging migration alone forces ~4亿 additional provision
- 2025 credit impairment spiked 369% YoY (1.23→5.76亿), total impairments = 35% of net profit
- Top-5 counterparty FOX Company: 21亿 AR at only 0.3% provision — severely under-provisioned
- Contract assets (119亿) nearly equal to AR (118亿), but provisioned at only 6.8%

**Verdeliverable:** "你卖出中材国际的决定是正确的" — AR is a structural feature of EPC business models, not a temporary cyclical issue, and it will continue to compress ROE and suppress valuation until the industry recovers or the company's business mix shifts toward recurring services.
