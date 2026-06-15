# Reverse-Engineering Hidden Metrics from Disclosed Ratio Formulas

## When to Use

When annual reports disclose a **ratio formula** but not the underlying components, you can often reverse-engineer the missing denominator (or numerator) if you know the other components from other parts of the report.

## Technique

### Step 1: Identify disclosed ratio formulas

Look for footnotes with definitions like:
- "净服务费率 = (A收入 + B收入 - 佣金) / 交易平台业务融资额"
- "净息差 = 经调整平均收益率 - 平均成本率"
- "拨备覆盖率 = 拨备余额 / 不良贷款余额"

### Step 2: Extract known components

From other parts of the annual report (income statement, MD&A, notes), extract the known variables.

### Step 3: Reverse-engineer the missing variable

Example from Yixin Group (02858.HK) 2024-2025:

**Disclosed formula:**
> 净服务费率 = (贷款促成收入 + SaaS收入 - 佣金) / 交易平台业务融资额

**Known components (from income statement + cost breakdown):**
| Year | Net Service Fee Rate | Loan Facilitation Revenue | SaaS Revenue | Commission |
|------|---------------------|--------------------------|--------------|------------|
| 2024 | 3.7% | 43.18亿 | 18.04亿 | 41.38亿 |
| 2025 | 5.1% | 25.46亿 | 45.01亿 | 40.45亿 |

**Reverse-engineered:**
```
2024: 3.7% = (43.18 + 18.04 - 41.38) / Platform Financing Volume
      3.7% = 19.84 / Platform Financing Volume
      Platform Financing Volume = 19.84 / 0.037 = 536亿

2025: 5.1% = (25.46 + 45.01 - 40.45) / Platform Financing Volume
      5.1% = 30.02 / Platform Financing Volume
      Platform Financing Volume = 30.02 / 0.051 = 589亿
```

### Step 4: Validate with cross-checks

- Total financing volume (2024: 691亿, 2025: 751亿) minus reverse-engineered platform volume (536亿, 589亿) = self-operated volume (~155亿, ~162亿)
- Check if self-operated volume is reasonable given self-operated transaction counts (16.2万笔 in 2024) and average loan size

### Step 5: Derive additional metrics

With reverse-engineered platform financing volume, calculate:
- **Comprehensive fee rate (including commission)** = (Loan Facilitation Revenue + SaaS Revenue) / Platform Financing Volume
  - 2024: 61.22 / 536 = **11.4%**
  - 2025: 70.47 / 589 = **12.0%**
- Compare with net service fee rate (excluding commission): 3.7% → 5.1%

## Pitfalls

1. **Ensure formula components match the reporting period** — some ratios use average balances, others use period-end balances
2. **Commission/cost allocation** — verify that "佣金" in the formula refers to the same cost item disclosed elsewhere
3. **Business segment reclassification** — if the company changed segment definitions mid-period, historical comparisons may not be apples-to-apples
4. **Rounding** — disclosed percentages may be rounded, causing small errors in reverse-engineered values. Use the most precise figures available.

## Application: Detecting Business Segment "Skin Changes"

When a company introduces a new business segment (e.g., "SaaS"), check if it absorbed parts of old segments over time:

**Yixin Group case study:**
- 2022: SaaS just launched, revenue 1.22亿 (2% of total)
- 2023: SaaS revenue 4.63亿, rate 4.5%, commission 1.6% → **"real SaaS"** (matches industry peers like Cangu 3.9-4.2%)
- 2024: SaaS revenue 18亿, rate 8.6%, commission 4.9% → **mixed business** (loan facilitation reclassified into SaaS)
- 2025: SaaS revenue 45亿, rate 11.2%, commission 6.1% → **heavily mixed** (54% of fee is commission)

**Key insight:** The SaaS fee rate jump from 4.5% to 11.2% was NOT "technology premium" — it was business structure change. The net service fee rate (2.9% → 5.1%) tells the real story of underlying unit economics improvement.

**Lesson:** When a segment's fee rate diverges significantly from industry peers, investigate whether the segment definition changed, not just whether the business improved.
