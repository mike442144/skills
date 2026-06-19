# Financial Company Asset Quality Analysis

Methodology for analyzing asset quality of financial/fin-tech companies (融资租赁, 助贷平台, 消费金融, 小贷公司, etc.) during listed company research.

## When to Apply

Target company has any of these characteristics:
- Lending/financing as core business (自营融资, 融资租赁, 小额贷款)
- Platform model connecting borrowers and lenders (助贷, 贷款促成, SaaS金融科技)
- Provides credit guarantees or risk-sharing arrangements for partners
- Reports metrics like NPL, 逾期率, 拨备覆盖率, ECL

## Data Sources (Priority Order)

### 1. Annual Report Appendices (附注)

For financial services companies, CIQ's `Industry Specific` data (NPL, allowances, charge-offs, loan composition) is **not available** in the user's Google Sheets. Instead, extract these from the annual report's financial notes:

- **NPL / 逾期率 / 不良率**: Look in 预期信用损失 (ECL) notes or 风险管理 chapter
- **拨备覆盖率 / Provision coverage**: Calculate from 减值准备 and 不良贷款 figures
- **Loan composition**: Look in 贷款及垫款 notes (by type: Consumer, Commercial, Vehicle, etc.)
- **ECL three-stage data**: Look in 预期信用损失三阶段 model disclosure

Use `get_company_announcements` with keywords like `"不良贷款率 拨备覆盖率 预期信用损失"` to find the relevant appendix pages.

**Key fields to extract (from annual report, not CIQ):**
- Non-Performing Loans (NPL) / Non-Accrual Loans
- Specific Allowance (专项拨备)
- General Allowance (一般拨备)
- Charge-Offs Gross / Recovered / Net
- Loan composition by type (Consumer, Commercial, Vehicle, etc.)
- Impaired Loans
- Unearned Loan Discount

**Pitfall:** CIQ typically only has the LATEST year's data in Industry Specific. For historical trends, use other sources below.

### 2. Credit Rating Reports (联合资信/中诚信/大公国际)

For companies with onshore operating subsidiaries that issue bonds or have credit ratings, rating agency reports are a GOLD MINE for historical asset quality data:

**Search pattern:** `web_search("联合资信 <子公司名称> 主体评级报告")` or search `lhratings.com` directly.

**Key data points available (typically 3+ years of history):**
- 不良率 (NPL ratio) — year-by-year
- 拨备覆盖率 (provision coverage ratio) — year-by-year
- 杠杆倍数 (leverage)
- 应收融资租赁款净值
- 净资产收益率
- 短期债务/长期债务结构
- 流动性比率

**Real example (易鑫集团):**
- 联合资信 rated 上海易鑫融资租赁 (2024) and 上海畅途融资租赁 (2025)
- Both reports contained 3-year histories of 不良率 and 拨备覆盖率
- These are the onshore operating entities — data is more granular than the HK-listed parent's disclosures

**Pitfall:** Rating reports are for the subsidiary, not the listed parent. Verify the subsidiary is the main operating entity. Data may not perfectly reconcile with consolidated HKEX/annual report figures.

### 3. Annual Report Appendices (年报附注) — ECL Three-Stage Data

IFRS 9 / CAS 22 requires disclosure of Expected Credit Loss (ECL) by three stages. This is found in the annual report's financial risk management notes (typically 附注3 财务风险管理).

**Key data from ECL staging:**

| Stage | Meaning | Typical Loss Rate |
|-------|---------|-------------------|
| Stage 1 | 正常 (12-month ECL) | 1-3% |
| Stage 2 | 信用风险显著增加 (lifetime ECL) | 30-60% |
| Stage 3 | 已发生信用减值/不良 (lifetime ECL) | 40-70% |

**Migration analysis from ECL data:**
- If Stage 2 proportion is very low (<1%), it may indicate loans jump directly from Stage 1 to Stage 3 (insufficient early warning)
- Stage 3 loss rate reveals collateral recovery rate (higher = worse recovery)
- Compare annual charge-offs against NPL balance to assess write-off aggressiveness

**How to extract:** Use `get_company_announcements` with keywords like `"年报 预期信用损失 三阶段"` or `"年报 财务风险管理 信用风险"`. Alternatively, download the annual report PDF from irasia.com/HKEX and use pymupdf.

### 4. Off-Balance-Sheet Guarantees (表外担保/风险保证)

**Critical for platform/助贷 companies:** Many fin-tech platforms provide implicit or explicit guarantees to partner banks. This creates off-balance-sheet risk exposure.

**Where to find:**
- Annual report notes: search for "风险保证", "融资担保", "信用增级", "差额补足", "流动性支持"
- Look for: 风险保证负债 (risk guarantee liability), 表外项目 (off-balance-sheet items)

**Key metrics to extract:**
- 表外担保余额 vs 自营贷款余额 (off-balance-sheet guarantee balance vs on-balance-sheet loans)
- 年度拨备计提增量 (annual provision increase for guarantees)
- 担保费率 (guarantee fee rate = annual provision / average guarantee balance)

**Red flags:**
- Off-balance-sheet guarantees growing faster than on-balance-sheet loans
- Guarantee fee rate (annual) below 3% — likely insufficient for long-term equilibrium coverage
- Total provision coverage ratio (含表外) below 170%

## Analysis Framework

### Asset Quality Scorecard

| Dimension | 🟢 Good | 🟡 Watch | 🔴 Alert |
|-----------|---------|----------|----------|
| NPL ratio trend | Declining | Stable | Rising |
| Provision coverage | >200% | 150-200% | <150% |
| Net charge-off/NPL | <50% | 50-80% | >80% (may mask true NPL) |
| Stage 3 loss rate | <30% | 30-50% | >50% (poor collateral recovery) |
| Stage 2 proportion | 2-10% (healthy warning) | <1% (insufficient early warning) | >15% (deteriorating portfolio) |
| Off-BS guarantees / On-BS loans | <1x | 1-2x | >2x and growing |
| Total coverage (含表外) | >200% | 170-200% | <170% |
| Guarantee fee adequacy | >3% cumulative | 2-3% | <2% (underpriced risk) |

### Industry Benchmarks (China, as of 2025)

| Institution Type | Typical NPL | Typical Coverage |
|-----------------|-------------|-----------------|
| 商业银行 (auto loans) | 0.5-1.0% | 200-300% |
| 汽车金融公司 (e.g., 上汽通用汽车金融) | ~0.25% | 200%+ |
| 融资租赁 (auto, investment-grade) | 1.5-3.0% | 150-200% |
| 助贷平台 (subprime-adjacent) | 1.5-3.0% | 150-200% |
| 消费金融公司 | 1.0-2.5% | 150-250% |

### Key Calculations

```
拨备覆盖率 = Total Allowance / NPL
拨备/总贷款 = Total Allowance / Total Loans (credit cost buffer)
净核销率 = Net Charge-Offs / Total Loans
隐含迁徙率 ≈ (Net Charge-Offs + ΔStage3) / Total Loans

全口径风险敞口 = 自营贷款 + 表外担保余额
全口径拨备覆盖率 = (自营ECL拨备 + 风险保证负债) / (自营NPL + 表外估计不良)

担保费率充足性 = 累计风险保证负债 / 表外担保余额
  - 需要 >3% 才能维持 ~200% 覆盖率
  - 年度增量拨备/平均担保余额 通常只有 1-2%
```

## Case Study: 易鑫集团 (02858.HK) — 2024-2025

**Key findings from this analysis:**

1. Off-balance-sheet guarantees (739亿) were 2.54x on-balance-sheet loans (291亿) at end-2024
2. Annual guarantee provision jumped +309% (1.58亿 → 6.46亿) — management recognizing risk
3. Stage 3 loss rate = 52.28% — vehicle collateral recovery very poor
4. Net charge-off/NPL = 80% — aggressive write-offs keep NPL ratio looking better than reality
5. Total provision coverage ~196% at end-2024, likely declining to ~170% in 2025 as SaaS-driven guarantees outpace provisioning
6. CIQ Industry Specific sheet confirmed: NPL 6.16亿, Total Allowance 11.48亿, Net Charge-offs 4.92亿 (FY2025)

**Data sources used:**
- CIQ Industry Specific sheet (FY2025 snapshot)
- 联合资信 rating reports for 上海易鑫融资租赁 (2024) and 上海畅途融资租赁 (2025) — 4-year history of NPL and coverage
- 2024 annual report appendices (ECL 3-stage data, off-balance-sheet guarantees)
- Broker reports (东吴/国金/国联民生) for cross-validation
