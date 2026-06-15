# Revenue Quality Diagnostics: Is the "Transformation" Real?

## When to Apply

Use this framework when a company claims a strategic business model transformation, especially:
- "From lending/助贷 to SaaS/金融科技"
- "From manufacturing to platform/service"
- "From product sales to subscription/recurring"
- "Light-asset transformation" (轻资产转型)
- "Digital transformation" / "科技赋能"

The user is likely to ask probing questions about whether the transformation is genuine. Have the analysis ready proactively.

## Three Diagnostic Tests

### Test 1: Fee Rate / Take Rate Benchmarking

**Logic**: Different business models have characteristic take rates. If a company claims "pure tech/SaaS" but its effective fee rate is far above the industry norm for pure tech services, the label is likely repackaging.

**Method**:
1. Calculate the company's effective take rate: revenue from the claimed "new" business ÷ underlying transaction volume (GMV, financing amount, etc.)
2. Search for pure-play peers in the SAME claimed business model and their take rates
3. If the target's rate is >2x the peer norm, the "transformation" likely includes hidden components (channel commissions, risk premium, implicit guarantees)

**Industry Take Rate Benchmarks (Chinese market, as of 2025)**:

| Business Model Label | Typical Take Rate | Source/Notes |
|---------------------|-------------------|--------------|
| Pure auto-lending facilitation (助贷) | 3-5% | 灿谷(CANG) 3.9-4.2%, 信也(FINV) 3.3-3.4% |
| Consumer lending tech platform (助贷) | 3-8% | 奇富(QFIN) ~5-7% blended, 乐信(LX) ~6-8% |
| Pure SaaS (software subscription) | N/A (fixed fees, not %-of-GMV) | Salesforce model; if charged as % of GMV, typically <3% |
| Payment processing | 0.3-1.5% | Alipay, WeChat Pay, UnionPay |
| E-commerce marketplace commission | 2-8% | Taobao/Tmall 2-5%, JD 4-8%, Pinduoduo ~3% |
| Insurance brokerage | 5-15% | Varies by product line |
| Real estate brokerage | 1-3% | Lianjia/Beike ~2-2.5% |
| Supply chain finance | 0.5-2% | Bank-originated; platforms charge premium for risk |

**Red flag threshold**: If the claimed "tech/SaaS" take rate exceeds 2x the pure-play peer norm, investigate what's embedded in the fee.

**Case study — 易鑫集团 (02858.HK)**:
- Claims: SaaS/金融科技 business, "outputting risk control capabilities to financial institutions"
- Effective take rate: 11.2% (45亿 revenue ÷ 403亿 facilitated financing)
- Pure auto-lending peer benchmark: 灿谷 3.9-4.2%, 信也 3.3-3.4%
- Gap: 11.2% vs 3-4% = ~3x industry norm
- Conclusion: ~60-70% of the "SaaS" revenue is likely repackaged lending facilitation (channel commissions + risk premium), with only 30-40% being genuine technology value-add

### Test 2: Cost Structure Consistency

**Logic**: A truly asset-light tech/SaaS business should have low marginal costs. If cost-of-revenue remains high (70%+), the business model hasn't actually transformed.

**Method**:
1. Find the cost-of-revenue (营业成本/服务成本) for the claimed "new" business segment
2. Calculate the gross margin
3. Compare against pure-play peers in the same claimed business model

**Benchmark gross margins by business model**:

| Business Model | Typical Gross Margin |
|---------------|---------------------|
| Pure SaaS / software | 70-90% |
| Technology platform (marketplace) | 60-80% |
| Lending facilitation / 助贷 (no risk) | 50-70% |
| Lending facilitation with risk/guarantee | 30-50% |
| Traditional financial services | 40-60% |
| Manufacturing | 15-40% |
| Distribution / trading | 5-20% |

**Red flag**: If a company claims "SaaS transformation" but the segment's gross margin is <50%, or the overall platform business cost ratio is >70%, the transformation is superficial.

**Case study — 易鑫集团**:
- Claims: SaaS/金融科技 is "asset-light tech"
- Actual: Platform business cost-of-services = 73.8亿 ÷ 92.9亿 revenue = 79.4% cost ratio
- Implied gross margin: ~20.6% (far below pure SaaS 70-90%)
- Conclusion: Cost structure is inconsistent with "pure tech" — heavy channel/operational costs remain

### Test 3: Revenue Migration Pattern

**Logic**: When one revenue line declines while a new "transformed" line grows by a similar absolute amount, suspect reclassification rather than genuine new business.

**Method**:
1. Compare the absolute decline in the "old" business vs. the absolute growth in the "new" business
2. If new growth ≈ old decline, the new business may just be the old business relabeled
3. Check: are the same customers, channels, and counterparties involved?

**Red flag patterns**:
- Old line drops X, new line grows by ~X (zero-sum migration)
- Company does not disclose overlap between old and new customer bases
- Company does not disclose whether the same channel network serves both lines
- Management uses vague language like "strategic adjustment" or "business structure optimization" without explaining the mechanics

**Case study — 易鑫集团**:
- 贷款促成 (old lending facilitation): 2024 ~43亿 → 2025 25.5亿 (decline ~18亿)
- SaaS (new "tech" business): 2024 ~18亿 → 2025 45亿 (growth ~27亿)
- Observation: SaaS growth (27亿) is in the same ballpark as 贷款促成 decline (18亿), suggesting migration rather than pure new creation
- Missing disclosure: No data on customer overlap, channel overlap, or funder overlap between the two lines

## How to Present Findings in the Report

When the diagnostics reveal that a "transformation" is partially repackaging:

1. **State the facts neutrally**: "SaaS revenue grew 150% to 45亿元, while loan facilitation revenue declined 41% to 25.5亿元."
2. **Present the benchmark**: "Pure auto-lending facilitation platforms typically earn 3-5% take rates (灿谷 3.9-4.2%, 信也 3.3-3.4%). The company's 11.2% SaaS take rate exceeds this range by 2-3x."
3. **Offer the interpretation**: "This suggests the SaaS fee includes components beyond pure technology licensing — likely channel commissions and risk-related pricing. Approximately 60-70% of SaaS revenue may represent repackaged lending facilitation."
4. **Note what's missing**: "The company has not disclosed customer overlap between the two business lines, or whether the same dealer network serves both."
5. **Let the reader draw conclusions**: Do not say "the company is misleading investors." Present the evidence and let the reader judge.

## Additional Diagnostic: Regulatory Exposure

When a "transformation" involves reclassifying regulated activities (e.g., lending → tech), assess regulatory risk:
- Does the new label escape the regulatory perimeter that applied to the old business?
- Are regulators catching up? (e.g., China's 2025 助贷新规 requiring fee transparency, capping comprehensive borrowing costs at 24%)
- If the "new" business is economically identical to the "old" but labeled differently, regulatory reclassification risk is high

**Case study**: China's 2025 助贷新规 (effective Oct 2025) requires:
- All credit facilitation fees (including "增信服务费") counted toward borrower's comprehensive cost
- Comprehensive cost capped at 24% annualized
- Banks must implement whitelist management of platform partners
- Fee payment progress must match loan principal recovery progress

Impact on 易鑫: If the 11.2% SaaS fee includes implicit guarantee/risk components, the new regulation may force fee compression or business model restructuring.

## Search Patterns for Peer Benchmarking

When looking for peer take rates, use these search queries:
- `"[company name] 服务费率 take rate 2024 2025"`
- `"[industry] 助贷 服务费率 行业标准"`
- `"[company name] annual report revenue breakdown by segment"`
- For US-listed Chinese fintech: check 20-F filings for "net revenue per loan" or "take rate" disclosures
- Broker reports (券商研报) often calculate and compare take rates across peers — search `"[company] 研报 费率 对比"`
