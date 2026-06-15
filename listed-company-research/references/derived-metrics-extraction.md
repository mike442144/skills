# Extracting Management-Defined Derived Metrics from Annual Reports

## What Are Derived Metrics?

Annual report MD&A sections often contain **management-defined derived metrics** — ratios, rates, and unit economics that are NOT standard accounting line items but are constructed by management to tell a specific story about business performance.

**Examples:**
- 净服务费率 (net service rate) = (loan facilitation + SaaS revenue - commissions) / platform financing volume
- 净息差 (net interest margin) = adjusted average yield - average cost rate
- Take rate = revenue / GMV
- ARPU, LTV/CAC, unit economics
- 服务费率, 佣金率, 资金成本率

## Why They Matter

These metrics are often **more informative than raw financial numbers** because they:
1. Reveal business model structure (e.g., fee decomposition)
2. Enable year-over-year comparison of business quality (not just scale)
3. Come with explicit calculation formulas in footnotes — no ambiguity about what's included
4. Allow decomposition into components (e.g., total fee = tech fee + channel commission + risk premium)

## Extraction Method

### Step 1: Search for rate/percentage tables in MD&A

Use `pdftotext` + `grep` to search for key terms in downloaded annual reports:

```bash
pdftotext "年度报告.pdf" "temp.txt"
grep -B 5 -A 10 "費率\|费率\|淨.*率\|净.*率\|take rate\|margin\|ARPU" "temp.txt"
```

Common search terms for Chinese annual reports:
- 費率 / 费率 (fee rate)
- 淨服務費率 / 净服务费率 (net service rate)
- 淨息差 / 净息差 (net interest margin)
- 佣金率 (commission rate)
- 資金成本率 / 资金成本率 (funding cost rate)
- 收益率 (yield rate)
- 滲透率 / 渗透率 (penetration rate)

### Step 2: Extract the calculation formula from footnotes

Every derived metric should have a footnote defining the calculation. **This is the most important part** — the formula tells you exactly what's included and excluded.

Example from 易鑫集团 2025 annual report:
> 淨服務費率(1)
> 附註：(1) 按貸款促成服務及SaaS服務收入（扣除佣金）除以交易平台業務融資額計算。

This formula reveals:
- Numerator: loan facilitation + SaaS revenue, **minus commissions**
- Denominator: platform financing volume
- The "扣除佣金" (net of commissions) tells you there IS a commission component embedded in the gross figure

### Step 3: Build multi-year trend

**Critical:** Search ALL available annual reports for the same metric, not just the latest year. Management often introduces these metrics consistently year after year.

Example: 易鑫净服务费率 across 3 years:
| Year | Net Service Rate | Gross Fee Rate | Implied Commission |
|------|-----------------|----------------|-------------------|
| 2023 | 2.9% | 4.5% | 1.6% (36% of gross) |
| 2024 | 3.7% | 8.6% | 4.9% (57% of gross) |
| 2025 | 5.1% | 11.2% | 6.1% (54% of gross) |

This trend revealed that channel costs were rising (not falling), contradicting the "asset-light transformation" narrative.

### Step 4: Decompose and benchmark

Once you have the derived metric, decompose it against industry benchmarks:
- Compare against pure-play peers doing the same activity
- The gap between the company's rate and pure-play rates reveals embedded components
- Example: 易鑫 11.2% vs 灿谷 3.9-4.2% vs 信也 3.3-3.4% → the 7-8% gap = channel commission + risk premium

## Pitfalls

1. **Different companies define the same metric differently.** Always check the footnote formula. "Take rate" at Company A may include different components than "Take rate" at Company B.

2. **Management may change the formula without announcing it.** If a derived metric suddenly jumps, check whether the denominator or numerator definition changed.

3. **These metrics are management-defined, not audited.** The calculation is in the MD&A, not in the audited financial statements. They may use different rounding, timing, or scope than the audited numbers.

4. **Search across multiple years.** A single year's data point is far less useful than a 3-year trend. The trend reveals whether the business model is improving or deteriorating.

5. **Cross-reference with raw financials.** The derived metric should be consistent with the raw IS/BS numbers. If management says "net service rate improved" but gross margins are flat, something doesn't add up.

## When to Apply

- Any company with a platform/marketplace business model (take rate analysis)
- Financial companies with lending/financing operations (NIM, fee rate, commission rate)
- Companies claiming business model transformation (track whether the "new" metrics are genuinely different from the "old" ones)
- SaaS/subscription companies (ARPU, churn rate, LTV/CAC)
