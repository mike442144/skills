# Accounting Restatement (前期会计差错更正) Detection and Analysis

When a listed company restates prior-period financial data, the restated numbers can make a fundamentally sound company look like it collapsed overnight. This reference provides a detection workflow, analysis framework, and reporting guidance for accounting restatement events in A-share/HK/US companies.

## When This Pattern Triggers

- Wind/mx multi-year fundamentals show an extreme YoY discontinuity (e.g., revenue -40%+ or net profit -60%+) that cannot be explained by industry conditions alone
- A company's latest quarterly/annual report was filed late (delayed disclosure is often a signal)
- News mentions "会计差错更正", "会计政策变更", "追溯调整", or "财务重述"
- Management change (especially chairman/CEO) occurred near the restatement date

## Detection Workflow

### Step 1: Spot the Anomaly in Wind Data

When querying `get_stock_fundamentals` for multi-year data, watch for:
- Revenue dropping >40% YoY while the company is profitable and operating normally
- Net profit dropping >60% YoY with no obvious operational catastrophe (fire, regulatory shutdown, major litigation)
- The decline being far larger than the industry average or any single peer
- Balance sheet items (e.g., 其他流动负债, 合同负债) showing sudden dramatic increases that correspond to the income drop

### Step 2: Search for Restatement Announcements

```bash
# Search for restatement announcements
node scripts/cli.mjs call financial_docs get_company_announcements '{"query":"<公司名或代码>前期会计差错更正","top_k":5}'

# Also search for accounting policy changes (which may be mislabeled as error corrections)
node scripts/cli.mjs call financial_docs get_company_announcements '{"query":"<公司名或代码>会计政策变更收入确认","top_k":3}'
```

### Step 3: Identify the Accounting Substance

Determine WHAT changed in the accounting. Common patterns in Chinese listed companies:

| Change Type | What It Means | Business Implication |
|-------------|---------------|---------------------|
| **Revenue recognition timing** | From ex-warehouse (发货即确认) to sell-through (终端动销确认) | Channel inventory was being counted as "sold" — restatement removes this |
| **Expense capitalization vs period cost** | Moving R&D or financing costs between periods | Shifts profit between periods without changing total |
| **Lease/accounting standard adoption** | IFRS 16 / CAS 21 transition | Changes balance sheet structure (right-of-use assets/lease liabilities) |
| **Goodwill impairment timing** | Recognizing impairment in current period vs amortizing | Large one-time charge, may be "kitchen sinking" |
| **Consolidation scope change** | Adding/removing subsidiaries from consolidation | Can create artificial YoY changes |

### Step 4: Assess Market and Regulatory Controversy

Search financial news for market reaction and controversy:

```bash
node scripts/cli.mjs call financial_docs get_financial_news '{"query":"<公司名>会计差错更正 收入调整 质疑","top_k":5}'
```

Key controversy dimensions to assess:
- **Classification dispute**: Is it truly a 差错更正 (error correction) or a 政策变更 (policy change)? Policy changes require restating ALL historical periods; error corrections only need comparable-period adjustment. Companies may prefer the latter to avoid disclosing historical overstatement.
- **Investor litigation**: Are law firms soliciting claims? What is the proposed claim period?
- **Regulatory action**: Has CSRC/SFC/exchange issued an inquiry letter (问询函)?
- **Management timing**: Did the restatement coincide with a leadership change? A restatement that "cleans the slate" for new management is a red flag.
- **Auditor opinion**: Did the auditor issue an unqualified opinion on the restated statements? Were prior-period audit opinions changed?

### Step 5: Present BOTH Restated and Pre-Restated Data

Build a comparison table showing the adjustment:

| Period | Revenue (Restated) | Revenue (Pre-Restated) | Adjustment | Adjustment % |
|--------|-------------------|----------------------|-----------|-------------|
| 2025Q1 | X | Y | Y-X | (Y-X)/Y |
| 2025H1 | X | Y | ... | ... |
| 2025 9M | X | Y | ... | ... |
| 2025 FY | X | Y | ... | ... |

Critical reporting rules:
- **Never present only restated numbers** without explaining the adjustment — a reader who saw the original quarterly reports will think the company "collapsed"
- **Never present only pre-restated numbers** — the restated numbers are the audited, current view
- **Always warn about non-comparability**: If historical years (e.g., 2021-2024) were NOT restated, any multi-year time series that spans the restatement boundary is NOT comparable. Explicitly note: "2021-2024 data is based on pre-restatement口径; 2025 data is based on post-restatement口径; the two are not directly comparable."
- **Calculate base-effect-adjusted growth**: Post-restatement YoY comparisons are distorted. For 2026Q1 vs 2025Q1, calculate growth on BOTH adjusted and pre-restatement bases.

### Step 6: Flag the Base Effect on Future Comparisons

This is the most commonly missed analytical point. When the restatement lowers the base period:

- 2026Q1 YoY growth will look spectacular (e.g., +82%) because the 2025Q1 base was artificially lowered
- But if you use the pre-restated 2025Q1 base, 2026Q1 might actually be DECLINING
- Both growth rates should be reported: "Based on restated 2025Q1: +82.6%. Based on pre-restated 2025Q1: -45.7%."

This matters for: equity research reports, management's self-assessment of recovery, and investor expectations.

## Reporting Treatment in the Research Report

### In the Executive Summary / Core Conclusion
- Lead with the fact that a restatement occurred and its scale (e.g., "the company restated 2025 revenue downward by ¥30.3 billion")
- Characterize the accounting substance in one sentence
- Flag market controversy and regulatory uncertainty

### In Section 1 (Company Overview)
- Add a "Recent Accounting Restatement" subsection if the event is material
- Include the timeline of original disclosure, restatement announcement, and any regulatory follow-up

### In Section 2 (Business Deep Dive)
- Explain what business activity drove the restatement (e.g., channel inventory vs actual demand)
- Present both restated and pre-restated segment data if available

### In Section 5 (Risk Factors)
- Add "Restatement regulatory and litigation risk" as a HIGH priority risk
- Note whether historical periods remain unreconciled (creating a persistent comparability problem)
- Assess dividend sustainability if the restatement reduced reported earnings (dividend may exceed current-period earnings, funded from retained earnings)

### In Appendix
- Full restatement comparison table (adjusted vs pre-adjusted for each period)
- Accounting substance analysis
- Market/regulatory controversy summary
- Base-effect-adjusted forward growth calculation

## Worked Example: 五粮液 (000858.SZ) 2025 Restatement

### Background
On 2026-04-30 (the A-share annual report filing deadline), Wuliangye disclosed its 2025 annual report simultaneously with a 前期会计差错更正 announcement. The restatement changed revenue recognition from "ex-warehouse" (发货即确认收入) to "sell-through" (终端动销完成后确认收入).

### Scale of Restatement

| Period | Revenue (Pre-Restated) | Revenue (Restated) | Adjustment | Adjustment % |
|--------|----------------------|-------------------|-----------|-------------|
| 2025Q1 | ¥369.40亿 | ¥170.86亿 | ¥198.55亿 | 53.7% |
| 2025 9M | ¥609.45亿 | ¥306.38亿 | ¥303.07亿 | 49.7% |
| 2025 FY | — | ¥405.29亿 | — | -54.55% YoY |

| Period | Net Profit (Pre-Restated) | Net Profit (Restated) | Adjustment | Adjustment % |
|--------|--------------------------|---------------------|-----------|-------------|
| 2025 9M | ¥215.11亿 | ¥64.75亿 | ¥150.36亿 | 69.9% |
| 2025 FY | — | ¥89.54亿 | — | -71.89% YoY |

### Accounting Substance
The change moved revenue recognition from "when goods leave the factory warehouse to distributors" to "when distributors actually sell through to end consumers." The financial logic: payments already received from distributors but for goods not yet sold-through were reclassified from revenue to 其他流动负债 (other current liabilities, representing pre-payment obligations). Wuliangye's 其他流动负债 jumped from ¥10.57亿 (early 2025) to ¥278.27亿 (Q3 2025 end).

### Controversy Dimensions
- **Classification**: Officially classified as 差错更正, but accounting experts argue it's closer to 政策变更 (which would require restating ALL historical periods, not just 2025)
- **Management timing**: Restatement coincided with chairman 曾从钦 being placed under investigation (2026-02-28) for serious disciplinary violations; the annual report was signed by acting chairman 副董事长华涛
- **Investor litigation**: Multiple law firms (上海久诚、北京威诺) announced claim periods (2025-04-28 to 2026-05-01 purchase window)
- **Base effect**: 2026Q1 showed +82.57% net profit growth vs restated 2025Q1 — but would be -45.7% vs pre-restated 2025Q1
- **Dividend anomaly**: 2025 dividend of ¥200.14亿 exceeded reported net profit of ¥89.54亿 (223.5% payout ratio), funded from retained earnings

### Industry Implication
This event may signal a broader reckoning for the Chinese baijiu industry. The "ship-to-channel-as-revenue" model was an industry-wide practice. As terminal consumption slowed in 2025, the gap between shipped volume and actual consumption became unsustainable. Wuliangye (as the #2 player) was the first to formally adjust — other companies may face pressure to follow, potentially revealing that years of "growth" were partly channel stuffing rather than true demand.

### Data Comparability Warning
2021-2024 Wind data for Wuliangye is based on the PRE-restatement口径. 2025 data is POST-restatement. These are NOT directly comparable. Any multi-year trend chart spanning 2021-2025 mixes two different accounting bases and is misleading without explicit annotation.
