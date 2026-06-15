# Financial Company ECL Analysis Patterns

For listed financial companies (auto finance, leasing, fintech platforms, consumer finance), the annual report contains critical credit risk data that must be extracted systematically.

## 1. Credit Impairment Loss Breakdown (信用减值亏损明细)

### Where to Find It
- **Location:** Annual Report → MD&A (Management Discussion & Analysis) → "信用減值虧損" section
- **Format:** Table breaking down annual ECL provisions by asset type
- **HKEX reports:** Search for "信用減值虧損" or "預期信用損失撥備" in the PDF text

### Typical Table Structure (HK-listed)
```
截至12月31日止年度                    2025年        2024年      变动%
                                     人民币千元    人民币千元

应收融资拨备                         706,903       524,766     +34.7%
其他应收款项拨备                     878,743       382,996     +129.4%
风险保证负债拨备                     711,098       645,949     +10.1%
应收账款拨备                          35,756        11,668     +206.4%
总计                               2,332,500     1,565,379     +49.0%
```

### Key Metrics to Extract
1. **应收融资拨备** — ECL on on-balance-sheet loans/leases (自营业务)
2. **风险保证负债拨备** — ECL on off-balance-sheet guarantees (SaaS/平台担保业务)
   - This is CRITICAL for fintech platform companies — proves they bear credit risk
3. **其他应收款项拨备** — Watch for abnormal growth (may indicate recovery issues)
4. **应收账款拨备** — Usually small, but abnormal growth warrants attention

### Analysis Framework
- **Compare provision growth vs business growth:** If ECL grows 49% while financing volume grows 8.7%, asset quality is deteriorating
- **Off-balance-sheet vs on-balance-sheet:** For platform companies, compare 风险保证负债拨备 vs 应收融资拨备 to understand risk distribution
- **Historical trend:** Extract 3-5 years to identify cycles (e.g., 2020 pandemic spike, 2021 improvement, 2024-2025 deterioration)

## 2. Capital Provider Structure (资金方结构)

### What to Look For
For financial platform companies, identify:
- Types of capital providers: 银行 (banks), 融资租赁公司 (leasing companies), 金融租赁公司 (financial leasing), 主机厂 (OEMs)
- Number of partner institutions
- Breakdown of financing volume by capital provider type

### Common Data Gap
**IMPORTANT:** Annual reports typically do NOT disclose the specific breakdown of financing volume by capital provider type (e.g., "X% from banks, Y% from leasing companies"). They only disclose:
- Total number of partner institutions (e.g., "与逾75家各类银行、金融租赁公司及主机厂建立合作关系")
- Aggregate financing volume

### Why This Matters
- **Banks** have lower funding costs (2-2.5% for AAA-rated)
- **融资租赁公司** have higher funding costs (4-6%+ for non-AAA)
- If a platform's blended take rate is rising, it may indicate increasing reliance on higher-cost capital providers
- The shift from bank-dominated to diversified capital providers affects margin structure

### How to Infer
When explicit breakdown is unavailable:
1. Compare SaaS service fee rate trends (rising rate may indicate more leasing company partners)
2. Look for language like "资本提供者日益多元化" (increasingly diversified capital providers)
3. Check broker reports for investor day / management commentary data
4. Note as explicit data gap in the report

## 3. ECL Three-Stage Model Data

### Where to Find It
- **Location:** Annual Report → Notes to Financial Statements → "预期信用损失" or "ECL" section
- **Format:** Table showing Stage 1/2/3 distribution with loss rates

### Key Metrics
| Stage | Description | Typical Loss Rate |
|-------|-------------|-------------------|
| Stage 1 | 正常 (Normal) | 1-3% |
| Stage 2 | 信用风险显著增加 (Significant increase) | 30-60% |
| Stage 3 | 已发生信用减值 (Credit impaired) | 50-80% |

### Red Flags
- **Stage 3 loss rate increasing** (e.g., 52% → 60%): Vehicle depreciation accelerating, especially for NEVs
- **Stage 2 proportion rising**: Leading indicator of future Stage 3 migration
- **High net charge-off / NPL ratio** (~80%): Aggressive write-offs make NPL look better than reality

## 4. Auto Finance Platform-Specific Metrics

### Key Ratios to Calculate
1. **表外/自营比** = 表外担保余额 / 自营应收融资租赁款
   - Rising ratio = shifting risk off balance sheet
2. **全口径拨备覆盖率** = (自营ECL拨备 + 风险保证负债) / (自营贷款 + 表外担保)
   - Below 200% = potentially insufficient
3. **担保费率** = 风险保证负债余额 / 表外担保余额
   - Should be 3-5% for adequate coverage
4. **信用减值亏损/融资交易额** = Annual ECL / Annual financing volume
   - Rising ratio = deteriorating asset quality

### Industry Benchmarks
- Bank auto loan NPL rate: 0.5-1.0%
- Bank provision coverage: 200-300%
- Auto finance company provision coverage: 150-200%
- Stage 3 loss rate (auto): 40-70% (varies by vehicle type, NEV higher)

## 5. Data Sources Priority

1. **Annual Report MD&A** — Credit impairment loss breakdown, capital provider count
2. **Annual Report Notes** — ECL three-stage model, detailed provision movements
3. **Broker Reports** — May contain investor day data on capital provider structure
4. **Rating Agency Reports** (联合资信, 中诚信) — Detailed asset quality metrics for on-balance-sheet entities
5. **ABS Offering Documents** — Granular loan-level data for securitized pools

## Pitfalls

- **Currency confusion:** HK-listed companies report in RMB but may have HKD equivalents
- **Provision vs reserve:** 拨备 (provision) is annual flow; 损失准备 (allowance) is cumulative stock
- **Off-balance-sheet opacity:** Guarantee provisions may be embedded in multiple line items
- **Broker report limitations:** Most broker reports do NOT have capital provider breakdown — don't assume they do
- **Historical comparability:** Provision categories may be reclassified between years (check footnotes)
