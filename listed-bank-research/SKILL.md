---
name: listed-bank-research
description: This skill should be used when the user wants to conduct in-depth fundamental research on any listed bank in A-share (China) market, Hong Kong-listed (HKEX) bank stocks, or US-listed Chinese banks. Triggers include phrases like "研究XX银行", "分析XX银行基本面", "帮我看看XX银行", "XX银行深度分析", "研究银行股", "分析银行财报", or when the user provides a bank stock code/ticker and asks for analysis. The skill produces a comprehensive, neutral-analysis Markdown report covering bank business model, financial performance, asset quality, capital adequacy, profitability drivers, and risk factors. It does NOT include financial statement modeling, valuation, stock ratings, or investment recommendations.
metadata:
  author: Mike Chen
  version: '2.0'
---

# Listed Bank Research Skill

## Overview

This skill enables comprehensive fundamental research on listed banks (上市银行), producing detailed Markdown reports based on annual reports, interim reports, and quarterly disclosures. The research framework follows securities industry standards, covering business model analysis, financial statement decomposition, key performance indicators (KPIs), asset quality assessment, capital adequacy, and risk factor identification.

## When to Use This Skill

Activate this skill when the user:

- Asks to research/analyze a specific listed bank (e.g., "研究招商银行", "分析工商银行")
- Requests deep-dive fundamental analysis of a bank stock
- Provides a stock code/ticker and asks for comprehensive analysis
- Asks about bank financial performance, asset quality, or business outlook
- Uses trigger phrases: "研究XX银行", "分析XX银行基本面", "XX银行深度分析", "研究银行股", "银行财报分析"

## How to Use This Skill

### Step 1: Identify the Target Bank

1. Parse the bank name or stock code from user input
2. Map to the correct security identifier:
   - A-share: 6-digit code (e.g., 600036 for 招商银行)
   - HKEX: 5-digit code (e.g., 03968 for 招商银行)
   - Common bank name mappings:
     - 招商银行 = 600036.SH / 03968.HK
     - 工商银行 = 601398.SH / 01398.HK
     - 建设银行 = 601939.SH / 00939.HK
     - 中国银行 = 601988.SH / 03988.HK
     - 农业银行 = 601288.SH / 01288.HK
     - 交通银行 = 601328.SH / 03328.HK
     - 兴业银行 = 601166.SH
     - 平安银行 = 000001.SZ
     - 宁波银行 = 002142.SZ
     - 北京银行 = 601169.SH

### Step 2: Use the Bank Analysis Framework

Refer to `references/bank_analysis_framework.md` for the complete analytical framework and identify what information needs to be obtained.

### Step 3: Systematic Data Collection (CRITICAL — Do NOT skip)

**This is the most important step.** The #1 cause of uneven report quality is ad-hoc, unstructured data collection. You MUST follow this systematic approach.

#### 3.1 Mandatory Data Collection Checklist

Before writing any report section, collect data according to this priority-tiered checklist. Each item is marked with:
- **[P0]** = Must have. Report cannot be generated without this data.
- **[P1]** = Should have. If unavailable after 2 attempts, mark as "数据未披露" with explanation.
- **[P2]** = Nice to have. If unavailable, omit the field gracefully without placeholder.

**Tier 1 — Core Financials (P0, collect first):**
- [ ] 总资产 (Total Assets) — latest + 2 prior years
- [ ] 客户贷款总额 (Total Loans) — latest + 2 prior years
- [ ] 客户存款总额 (Total Deposits) — latest + 2 prior years
- [ ] 营业收入 (Operating Income) — latest + 2 prior years
- [ ] 归母净利润 (Net Profit attributable to shareholders) — latest + 2 prior years
- [ ] 净利润 (Net Profit) — latest + 2 prior years
- [ ] ROE — latest + 2 prior years
- [ ] ROA — latest + 2 prior years
- [ ] 净息差 NIM — latest + 2 prior years
- [ ] 不良贷款率 NPL Ratio — latest + 2 prior years
- [ ] 拨备覆盖率 Provision Coverage — latest + 2 prior years
- [ ] 资本充足率 CAR — latest
- [ ] 核心一级资本充足率 CET1 — latest

**Tier 2 — Business Structure (P0/P1, collect second):**
- [ ] 利息净收入 (Net Interest Income) [P0]
- [ ] 手续费及佣金净收入 (Net Fee Income) [P1]
- [ ] 投资收益 (Investment Income) [P1]
- [ ] 成本收入比 Cost-to-Income Ratio [P0]
- [ ] 对公贷款总额及占比 [P1]
- [ ] 零售贷款总额及占比 [P1]
- [ ] 对公存款总额 [P1]
- [ ] 零售存款总额 [P1]
- [ ] 个人客户数 / AUM [P2]
- [ ] 分支机构数量 [P2]
- [ ] 员工人数 [P2]

**Tier 3 — Asset Quality Detail (P1, collect third):**
- [ ] 不良贷款余额 (NPL Balance) [P0]
- [ ] 关注类贷款占比 Special Mention Ratio [P1]
- [ ] 贷款拨备率 Loan Provision Ratio [P1]
- [ ] 信用成本 Credit Cost [P2]
- [ ] 核销金额 Write-off Amount [P2]
- [ ] 逾期贷款分析 Overdue Loan Analysis [P2]
- [ ] 贷款迁徙率 Migration Rate [P2]
- [ ] 房地产行业贷款及不良率 [P1]
- [ ] 政信/LGFV 贷款规模 [P1]

**Tier 4 — Capital & Liquidity (P1, collect fourth):**
- [ ] 一级资本充足率 Tier-1 Capital Ratio [P1]
- [ ] 杠杆率 Leverage Ratio [P2]
- [ ] LCR [P2]
- [ ] NSFR [P2]
- [ ] 存贷比 LDR [P1]
- [ ] 每股分红 DPS [P1]
- [ ] 分红率 Payout Ratio [P1]

**Tier 5 — Peer Comparison (P1, collect fifth):**
- [ ] 选取 3-5 家可比银行（同类别城商行/股份行）
- [ ] 获取对比银行的：NIM、不良率、拨备覆盖率、ROE、总资产、净利润 [P1]

#### 3.2 Data Collection Strategy (Accounting for API Limits)

**When using mx-findata (or any tool with per-query limits):**
- Each query can return max 3 indicators × 5 entities
- Plan queries to maximize efficiency:
  - Query 1: 总资产 + 净利润 + 营业收入 for target bank (3 years)
  - Query 2: 不良率 + 拨备覆盖率 + NIM for target bank (3 years)
  - Query 3: ROE + ROA + 成本收入比 for target bank
  - Query 4: Peer comparison — pick 5 peers × 3 core metrics
  - Query 5: Peer comparison — remaining peers × remaining metrics
- Use `mx-finsearch` to fill gaps that structured data can't provide (e.g., loan industry breakdown, management commentary, regulatory penalties)

#### 3.3 Data Gap Handling Protocol

When a data field cannot be obtained after reasonable effort:

**Rule 1 — Never fabricate data.** If a number is not available, say so explicitly.

**Rule 2 — Tier-based handling:**
- **P0 data missing**: STOP. Attempt alternative sources (web_search, direct PDF extraction). If still unavailable, note in the report: "【数据缺口】该指标为必需数据，但未能从公开渠道获取。可能原因：银行未披露/数据源覆盖不全。"
- **P1 data missing**: Make 2 attempts from different sources. If still unavailable, write: "【数据未披露】XX银行未在年报中披露该指标" and proceed.
- **P2 data missing**: Omit the field gracefully. Do not include empty placeholders.

**Rule 3 — Use proxy data when direct data is unavailable:**
- If loan industry breakdown not disclosed → use broker research reports for estimates
- If LCR/NSFR not available for HKEX-listed banks → note that HKMA reporting requirements differ from mainland
- If credit cost not directly stated → calculate as: 信用减值损失 / 平均贷款余额

**Rule 4 — Cross-validation**: When data from different sources conflicts, prefer the annual report as the source of truth. Note discrepancies.

### Step 4: Compile the Research Report

Use the report template at `references/report_template.md` as the standard structure. The template follows a business-logic-driven organization (modeled after top brokerage research reports) rather than a financial-statement-driven layout. Key structural principles:

- **Organize by business logic, not by accounting line items.** Each section should follow the chain: conclusion → data → reasoning.
- **Embed peer comparisons within each section** rather than concentrating them in a single chapter.
- **Section II (Regional Economy)** is essential for city/rural commercial banks; may be condensed for nationwide banks.
- **Section III (Business Analysis)** is the core section — analyze each business line (Corporate / Retail / Financial Markets) with depth on strategy, products, client segments, and competitive advantage.
- **Section V (Asset Quality)** must include dedicated subsections for real estate and LGFV exposure with full-caliber quantification (on-balance-sheet loans + bond investments + non-standard + off-balance-sheet), trend analysis, and stress scenarios.
- **Section VII (Risk Factors)** is a key section of the report — each risk must be specific, quantified, and assessed for trajectory. Include dedicated deep-dive subsections for real estate credit risk, LGFV/local government debt risk, consumer credit risk, and market risk. Conclude with a Risk Summary Matrix.

**Section completeness rule:** Each section of the report must have at least one data table with actual numbers. If P0 data for a section is missing, that section must still be written using qualitative analysis from news/reports, with explicit notation of what data is unavailable.

### Step 5: Report Completeness Check (MANDATORY)

Before delivering the report, run this self-check:

1. **Data table audit**: Count data tables in the report. A proper report should have at least 8-10 tables. If fewer, identify which sections are missing tables and fill them.
2. **Placeholder scan**: Search for `[...]` or empty cells in tables. Every placeholder must be either filled or explicitly marked as "未披露" with a reason.
3. **Section balance check**: Each major section (I-VIII) should have roughly comparable depth. If one section is 500 words and another is 50 words, the shorter one needs more work.
4. **Peer comparison check**: Are there at least 3 peer banks referenced in the report? Are comparisons embedded in relevant sections?
5. **Risk section check**: Does Section VII have quantified exposures? Is the Risk Summary Matrix complete?

If any check fails, fix the report before delivery. Do NOT deliver an incomplete report.

### Step 6: Deliver the Report

1. Generate clean Markdown content
2. Include data tables where appropriate (financial metrics comparison)
3. Add charts/visualizations if data supports it (can be generated separately)
4. Cite data sources and report dates
5. At the end of the report, include a **Data Completeness Summary**:

```
## 数据完备性说明
| 数据类别 | 获取情况 | 备注 |
|---------|---------|------|
| 核心财务指标 (Tier 1) | 完整/部分缺失 | 列出缺失项 |
| 业务结构数据 (Tier 2) | 完整/部分缺失 | 列出缺失项 |
| 资产质量明细 (Tier 3) | 完整/部分缺失 | 列出缺失项 |
| 资本与流动性 (Tier 4) | 完整/部分缺失 | 列出缺失项 |
| 同行对比数据 (Tier 5) | 完整/部分缺失 | 列出缺失项 |
```

6. Retain perspectives that hold materialist dialectical value, and discard all other perspectives. A perspective with materialist dialectical value must satisfy the following:
   - Materialist Nature: The subject of the perspective is a certain fact, rather than a certain opinion. Predictions about changes in facts are also considered opinions.
   - Unity of Opposites: If the perspective holds, it should serve as a driving force to further iterate the original conclusion, enabling a more comprehensive exploration of the matter, rather than merely serving as supplementary incremental information.

## Key Metrics Reference

| Category | Metric | Calculation | Significance |
|----------|--------|-------------|--------------|
| Profitability | ROE | Net Income / Average Equity | Overall shareholder return |
| Profitability | ROA | Net Income / Average Total Assets | Asset efficiency |
| Profitability | Net Interest Margin | Net Interest Income / Avg Interest-Earning Assets | Core lending business profitability |
| Profitability | Cost-to-Income Ratio | Operating Expenses / Operating Income | Operational efficiency |
| Asset Quality | NPL Ratio | NPL / Total Loans | Loan portfolio quality |
| Asset Quality | Provision Coverage | Loan Loss Provisions / NPL | Loss absorption capacity |
| Asset Quality | Special Mention Ratio | Special Mention Loans / Total Loans | Potential deterioration indicator |
| Capital | CAR | Total Capital / Risk-Weighted Assets | Capital adequacy (regulatory minimum: 10.5%) |
| Capital | CET1 Ratio | Common Equity Tier-1 / RWA | Highest quality capital (regulatory minimum: 7.5%) |
| Liquidity | LDR | Loans / Deposits | Funding structure indicator |
| Liquidity | LCR | High-Quality Liquid Assets / 30-Day Net Cash Outflows | Short-term liquidity (regulatory minimum: 100%) |
| Liquidity | NSFR | Available Stable Funding / Required Stable Funding | Long-term liquidity (regulatory minimum: 100%) |

## Banking Sector Classification (China)

| Category | Banks | Market Character |
|----------|-------|------------------|
| Large State-Owned (国有大行) | ICBC, ABC, BOC, CCB, BoCOM, PSBC | Systemically important, nationwide coverage |
| Joint-Stock Banks (股份制银行) | CMB, SPDB, CITIC, Shanghai Pudong Development, etc. | National reach, retail focus |
| City Commercial Banks (城商行) |宁波银行, 北京银行, 南京银行, etc. | Regional focus, local government ties |
| Rural Commercial Banks (农商行) | 渝农商行, 青农商行, etc. | Rural county coverage |
