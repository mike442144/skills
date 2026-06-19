# Financial Institution Metrics Analysis

Reference guide for analyzing companies with significant financial/credit business lines using banking-style metrics.

## When to Use

Apply this framework when the target company has:
- Direct lending or leasing operations (auto finance, consumer lending, equipment leasing)
- Guarantee or credit enhancement business (SaaS with implicit guarantees, loan facilitation with recourse)
- Factoring or receivables financing
- Any business where credit risk is a material component

Many "tech platform" companies (e.g., auto finance platforms, fintech companies) have substantial financial business that should be analyzed separately from their technology metrics.

## Core Financial Metrics

### 1. Net Interest Margin (NIM / 净息差)

**Definition**: The spread between interest income earned and cost of funds paid, relative to interest-earning assets.

**Formula**:
```
NIM = (Interest Income - Cost of Funds) / Average Interest-Earning Assets
```

**For auto finance / leasing companies**:
```
NIM = (融资租赁服务收入 - 资金成本) / 季度平均应收融资租赁款
```

**Data sources**:
- Annual report MD&A section: Look for "利息收入", "资金成本", "净息差" tables
- Quarterly reports: May provide quarterly average balances
- CIQ Income Statement: Interest income line items
- CIQ Ratios: May have calculated margins

**Benchmarks**:
| Institution Type | Typical NIM Range |
|-----------------|-------------------|
| Commercial Banks (China) | 2.0-2.5% |
| Financial Leasing Companies | 2.5-3.5% |
| Auto Finance Companies | 4.0-7.0% |
| Consumer Finance Companies | 6.0-12.0% |

**Analysis points**:
- Higher NIM may indicate specialized lending (higher risk) or pricing power
- Declining NIM may signal competition, rate cuts, or asset quality deterioration
- Compare yield (收益率) and cost of funds (资金成本) separately to understand drivers
- Note: Some companies report "adjusted yield" (经调整平均收益率) excluding certain costs

### 2. Asset Quality Metrics

#### NPL Ratio (不良率)

**Definition**: Non-performing loans as a percentage of total loans.

**Formula**:
```
NPL Ratio = Non-Performing Loans / Total Loans
```

**Data sources**:
- CIQ Industry Specific sheet: "Non-Accrual Loans" or "Non Performing Loans"
- Annual report: Look for "不良贷款", "不良率", "逾期贷款"
- Regulatory filings: For licensed financial institutions

**Benchmarks**:
| Institution Type | Typical NPL Range |
|-----------------|-------------------|
| Commercial Banks (China) | 0.8-1.5% |
| Financial Leasing Companies | 1.5-2.5% |
| Auto Finance Companies | 1.5-3.0% |
| Consumer Finance Companies | 2.0-4.0% |

**Analysis points**:
- Rising NPL indicates deteriorating underwriting or economic stress
- Compare against industry peers and historical trend
- Note: Definition of "non-performing" may vary (90 days past due is common)

#### Provision Coverage Ratio (拨备覆盖率)

**Definition**: Total loan loss provisions as a percentage of non-performing loans.

**Formula**:
```
Provision Coverage = Total Allowance / Non-Performing Loans
```

**Data sources**:
- CIQ Industry Specific: "Specific Allowance" + "General Allowance" vs "Non-Accrual Loans"
- Annual report: Look for "拨备覆盖率", "减值准备", "预期信用损失"
- Calculate from balance sheet: Loan loss reserve / NPL

**Benchmarks**:
| Threshold | Interpretation |
|-----------|----------------|
| <150% | Below regulatory minimum (for banks in China) |
| 150-200% | Adequate but not comfortable |
| 200-300% | Healthy buffer |
| >300% | Very conservative or potentially hiding profits |

**Analysis points**:
- Low provision coverage indicates insufficient risk buffer
- Rising coverage may signal prudence or deteriorating outlook
- Compare "specific allowance" (专项拨备) vs "general allowance" (一般拨备)
- Note: Some companies report ECL (Expected Credit Loss) under IFRS 9 / CAS 22

#### Provision Ratio (拨备/总贷款)

**Definition**: Total provisions as a percentage of total loans.

**Formula**:
```
Provision Ratio = Total Allowance / Total Loans
```

**Benchmarks**:
- Banks: 1.5-3.0%
- Leasing companies: 2.0-4.0%
- Auto finance: 2.5-5.0%

#### Net Charge-Off Rate (净核销率)

**Definition**: Net charge-offs (gross charge-offs minus recoveries) as a percentage of total loans.

**Formula**:
```
Net Charge-Off Rate = Net Charge-Offs / Total Loans
```

**Data sources**:
- CIQ Industry Specific: "Charge-Offs - Net"
- Annual report: Look for "核销", "撇销"

**Analysis points**:
- High charge-off rate indicates actual losses materializing
- Compare against NPL ratio: High charge-off/NPL ratio means aggressive write-offs
- Note: Charge-offs reduce both NPL and provisions

### 3. ECL Three-Stage Analysis (IFRS 9 / CAS 22)

**Definition**: Expected Credit Loss model classifies loans into three stages based on credit risk:
- **Stage 1 (正常)**: 12-month ECL, loans performing normally
- **Stage 2 (关注/信用风险显著增加)**: Lifetime ECL, significant increase in credit risk
- **Stage 3 (不良/已发生信用减值)**: Lifetime ECL, credit-impaired assets

**Data sources**:
- Annual report notes (附注): Look for "预期信用损失", "三阶段", "ECL"
- Typically in "金融风险管理" or "信用风险" sections
- May include: balance by stage, loss rate by stage, ECL provision by stage

**Key metrics to extract**:
| Metric | What It Tells You |
|--------|------------------|
| Stage 3 / Total (不良率) | Current NPL ratio |
| Stage 2 / Total (关注类占比) | Early warning indicator |
| Stage 3 Loss Rate (Stage 3损失率) | Severity of losses once defaulted |
| Stage migration (迁徙) | Whether assets are deteriorating |

**Analysis points**:
- **Stage 3 loss rate** is critical: High loss rate (e.g., >50%) means poor recovery rates
- **Stage 2 increase** may predict future Stage 3 growth
- Compare loss rates across years to spot deterioration
- Note: Loss rate = ECL Provision / Stage Balance

**Example extraction** (from annual report):
```
| 阶段 | 账面余额（亿元） | 占比 | 损失率 | ECL拨备（亿元） |
|------|----------------|------|--------|----------------|
| Stage 1 | 317.67 | 97.45% | 2.09% | 6.64 |
| Stage 2 | 2.17 | 0.66% | 52.39% | 1.14 |
| Stage 3 | 6.16 | 1.89% | 60.00% | 3.69 |
| 总计 | 326.00 | 100% | 3.52% | 11.47 |
```

### 4. Leverage & Capital Adequacy

#### Leverage Ratio (杠杆倍数)

**Definition**: Total assets divided by equity, showing how much assets are financed by equity vs debt.

**Formula**:
```
Leverage Ratio = Total Assets / Total Equity
```

**Benchmarks**:
| Institution Type | Typical Leverage |
|-----------------|------------------|
| Commercial Banks | 10-15x |
| Financial Leasing Companies | 6-10x |
| Auto Finance Companies | 4-8x |
| Insurance Companies | 5-10x |

**Analysis points**:
- Higher leverage = more efficient capital use but higher risk
- Low leverage may indicate underutilized capital or conservative management
- Compare against regulatory requirements (e.g., banks have capital adequacy ratios)
- Note: Some companies have off-balance-sheet exposures (guarantees, commitments)

#### Debt-to-Equity Ratio

**Formula**:
```
Debt-to-Equity = Total Debt / Total Equity
```

**Analysis points**:
- Focus on interest-bearing debt (borrowings, bonds, leases)
- Compare debt maturity profile against asset maturity (match funding)
- Note asset-backed securities (ABS) as a funding source

#### Asset-Liability Ratio (资产负债率)

**Formula**:
```
Asset-Liability Ratio = Total Liabilities / Total Assets
```

**Benchmarks**:
- Banks: 90-95% (high leverage is normal)
- Leasing companies: 70-85%
- Auto finance: 65-80%

### 5. Profitability Metrics

#### ROA (Return on Assets / 总资产收益率)

**Formula**:
```
ROA = Net Income / Average Total Assets
```

**Benchmarks**:
| Institution Type | Typical ROA |
|-----------------|-------------|
| Commercial Banks (China) | 0.5-1.0% |
| Financial Leasing Companies | 1.0-2.0% |
| Auto Finance Companies | 1.5-3.0% |
| Consumer Finance Companies | 2.0-4.0% |

**Analysis points**:
- ROA is the ultimate measure of asset efficiency
- Higher ROA may indicate pricing power, operational efficiency, or higher risk
- Compare against cost of capital to assess value creation

#### ROE (Return on Equity / 净资产收益率)

**Formula**:
```
ROE = Net Income / Average Total Equity
```

**Benchmarks**:
- Banks: 8-12%
- Leasing companies: 8-15%
- Auto finance: 10-20%

**Analysis points**:
- ROE = ROA × Leverage (DuPont decomposition)
- High ROE with low leverage = superior profitability
- High ROE with high leverage = amplified by debt
- Compare against cost of equity (typically 10-15%)

## Comparison Framework

When analyzing a company with financial business, create a comparison table:

| Metric | Target Company | Commercial Banks | Financial Leasing | Auto Finance | Industry Average |
|--------|---------------|------------------|-------------------|--------------|------------------|
| NIM | X% | 2.0-2.5% | 2.5-3.5% | 4.0-7.0% | Y% |
| NPL Ratio | X% | 0.8-1.5% | 1.5-2.5% | 1.5-3.0% | Y% |
| Provision Coverage | X% | 150-300% | 150-250% | 150-250% | Y% |
| Leverage | Xx | 10-15x | 6-10x | 4-8x | Yx |
| ROA | X% | 0.5-1.0% | 1.0-2.0% | 1.5-3.0% | Y% |
| ROE | X% | 8-12% | 8-15% | 10-20% | Y% |

## Data Extraction Checklist

### From Annual Reports (MD&A Section)
- [ ] Interest income (利息收入) and average yield (平均收益率)
- [ ] Cost of funds (资金成本) and average cost rate (平均成本率)
- [ ] Net interest margin or spread (净息差/净利差)
- [ ] Quarterly average interest-earning assets (季度平均生息资产)
- [ ] NPL ratio (不良率) and definition
- [ ] Provision coverage ratio (拨备覆盖率)
- [ ] Charge-offs and recoveries (核销/撇销)
- [ ] Overdue loan buckets (逾期贷款分布: 30/60/90/180 days)

### From Annual Report Notes (附注)
- [ ] ECL three-stage breakdown (三阶段分布)
- [ ] Loss rates by stage (各阶段损失率)
- [ ] Provision movements (减值准备变动表)
- [ ] Loan portfolio composition (贷款组合结构)
- [ ] Collateral coverage (抵押物覆盖率)

### From CIQ Financials
- [ ] Industry Specific sheet: NPL, allowances, charge-offs
- [ ] Balance Sheet: Total loans, total provisions
- [ ] Income Statement: Interest income, interest expense
- [ ] Ratios: ROA, ROE, leverage ratios

### From Regulatory Filings (if applicable)
- [ ] Capital adequacy ratios (资本充足率)
- [ ] Regulatory returns (监管报表)
- [ ] Examination results (检查结果)

## Common Pitfalls

1. **Off-balance-sheet exposures**: Companies may have significant guarantees, commitments, or contingent liabilities not reflected in leverage ratios. Always check for "表外业务" or "担保余额".

2. **ECL model differences**: Loss rates under IFRS 9 / CAS 22 are forward-looking and may differ significantly from actual losses. Compare Stage 3 loss rates across companies to assess model conservatism.

3. **Definition of NPL**: Some companies use 90-day past due, others use 180-day or different criteria. Always check the definition and adjust for comparability.

4. **Seasonality**: Auto finance and consumer lending may have seasonal patterns. Use average balances (quarterly or annual) rather than period-end balances for ratio analysis.

5. **Funding structure**: Companies without deposit bases (unlike banks) rely on wholesale funding, ABS, or bank borrowings. Cost of funds may be more volatile.

6. **Related-party transactions**: Some financial companies have significant lending to related parties. Check for concentration risk and arm's-length pricing.

7. **Regulatory arbitrage**: Companies may structure products to avoid classification as "lending" (e.g., "service fees" instead of interest). Look at economic substance over legal form.

## Case Study: Yixin Group (02858.HK)

**Key findings from 2025 annual report**:
- NIM: 6.0% (Yield 9.7% - Cost 3.7%) — well above banks, typical for auto finance
- NPL: 1.81% — within range for auto finance companies
- Provision coverage: 186.4% — below 200% safety threshold
- Stage 3 loss rate: 60.00% — very high, indicates poor recovery rates for defaulted auto loans
- Leverage: 5.3x — conservative vs banks (10-15x), suggests underutilized capital
- ROA: 2.33% — strong, above most financial institutions
- ROE: 7.17% — moderate, limited by conservative leverage

**Insights**:
- High NIM reflects specialized auto lending risk and pricing power
- Stage 3 loss rate of 60% is concerning — vehicle depreciation and disposal challenges
- Low leverage suggests room for growth but also conservative capital management
- Provision coverage below 200% indicates limited buffer for deterioration

## Further Reading

- IFRS 9 / CAS 22: Financial Instruments (ECL model)
- Basel III: Capital adequacy framework for banks
- PBOC regulations: Loan classification and provisioning requirements
- CBIRC guidelines: Risk management for financial institutions

## Fintech Platform Funding Source Analysis

Annual reports often disclose the NUMBER of cooperation partners (e.g., "近75家各类银行、金融租赁公司及主机厂") but NOT the proportion breakdown by institution type (banks vs leasing vs consumer finance). This is a common data gap.

**Workarounds:**
1. "资本提供者日益多元化" or similar language signals a shift from banks (lower funding cost) to leasing companies (higher funding cost) — infer from fee rate changes
2. Look for "核心客户" (core customer) data — number and average revenue per core customer can indicate concentration
3. Check investor presentations or earnings call transcripts for more detailed breakdowns
4. If critical to the analysis, note as data gap and recommend contacting IR

**Funding cost differentials (auto finance platforms, China 2025):**

| Institution Type | Funding Cost |
|-----------------|-------------|
| Banks | 2.0-2.5% |
| AAA-rated leasing companies | 2.3-2.5% |
| AA+ rated | 2.8-3.5% |
| Small/medium leasing | 4-6%+ |

A shift toward leasing companies as funding sources will push up the platform's 综合费率 (comprehensive fee rate).

**"Tech platform" pitfall**: Companies like Yixin may present as "tech companies" but have substantial credit risk exposure through guarantees, SaaS services with implicit guarantees, or direct lending. Analyze BOTH tech platform metrics (GMV, take rate, merchant count) AND financial institution metrics (NIM, NPL, provision coverage). The two analyses may reveal different risk profiles.
