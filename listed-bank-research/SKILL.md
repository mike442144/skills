---
name: listed-bank-research
description: This skill should be used when the user wants to conduct in-depth fundamental research on any listed bank in A-share (China) market, Hong Kong-listed (HKEX) bank stocks, or US-listed Chinese banks. Triggers include phrases like "研究XX银行", "分析XX银行基本面", "帮我看看XX银行", "XX银行深度分析", "研究银行股", "分析银行财报", or when the user provides a bank stock code/ticker and asks for analysis. The skill produces a comprehensive, neutral-analysis Markdown report covering bank business model, financial performance, asset quality, capital adequacy, profitability drivers, and risk factors. It does NOT include financial statement modeling, valuation, stock ratings, or investment recommendations.
metadata:
  author: Mike Chen
  version: '2.4'
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
     - 南京银行 = 601009.SH
     - 杭州银行 = 600926.SH
     - 成都银行 = 601838.SH
     - 江苏银行 = 600919.SH
     - 长沙银行 = 601577.SH
     - 重庆银行 = 601963.SH / 01963.HK
     - 贵阳银行 = 601997.SH
     - 郑州银行 = 002936.SZ / 06196.HK
     - 青岛银行 = 002948.SZ / 03866.HK
     - 苏州银行 = 002966.SZ
     - 齐鲁银行 = 601665.SH
     - 厦门银行 = 601187.SH

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
- [ ] 政信/LGFV 贷款规模 [P1] — 估算方法：F9银行贷款结构中"租赁和商务服务业"+"水利环境和公共设施管理业"贷款余额合计作为代理指标，详见 references/ifind_f9_bank_data_model.md

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

> ⚠️ **数据口径一致性要求（最高优先级）：** 同行对比时，必须确保数据口径一致。
> - **期间指标（ROE、NIM、净利润增速等）**: 年报全年 vs 年报全年，或三季报累计 vs 三季报累计。严禁将全年数据与三季报/中报数据直接比较排名。银行盈利存在季节性（Q4通常为利润结算高峰期），三季报累计ROE/NIM不等于全年值。
> - **时点指标（不良率、拨备覆盖率、总资产、CAR等）**: 可比性较强，但需标注具体时点（如"2025年末" vs "2025年9月末"）。
> - **必须在表格中标注每家银行的数据来源口径**（如"年报全年"、"3Q累计"、"中报"）。
> - **基于混合口径数据不得得出排名或定性结论**（如"领先"、"仅次于"、"偏低"等）。如果口径不一致，应保留数据但不下结论，或明确说明口径差异。

#### 3.2 Data Collection Strategy (Accounting for API Limits)

**Annual Report Download & MD&A Review [P0]:** Download the latest 10 years of annual reports and perform a multi-year MD&A review per `references/multi-year-mda-review.md` (feeds §I.4, IV.1, V.1, VIII).
- A-share: `node ~/Projects/tinyant/cninfo/index.js --codes <6-digit> --annual --year <start-end>`
- HKEX: `node ~/Projects/tinyant/hkexnews/index.js --codes <5-digit> --year <start-end>`

**Tool hierarchy for bank research (proven by execution):**

| Priority | Tool | Best For | Notes |
|----------|------|----------|-------|
| 1st | **ifind-repilot-finance-data-search** (同花顺 F9 银行模块) | Bank-specific deep data: loan structure by industry with NPL rates, migration rates, capital composition, deposit structure, dividend history, employee/management details, NIM decomposition | The F9 outputs `F9-{bank}-银行贷款结构`, `F9-{bank}-银行分析指标`, `F9-{bank}-银行存款结构` are GOLD MINES — far richer than any other source for bank research. See `references/ifind_f9_bank_data_model.md` for the full query map. |
| 2nd | **Wind MCP** (`get_stock_fundamentals`) | Core 3-year financials (总资产/营收/净利润/ROE/NIM/不良率/拨备覆盖率/CAR/CET1), peer comparison across multiple banks, basic info, shareholders, events | Good for multi-year trends and peer side-by-side. BUT: often returns `None` for bank-specific metrics (ROA, RE exposure, fee breakdown). Unit inconsistency warning: total assets/deposits may come back in "万亿元" (e.g., 1.84) while loans come back in "亿元" (e.g., 8070.96) — ALWAYS verify units. |
| 3rd | **mx-finance-data** (东方财富) | Fallback if Wind/ifind unavailable or rate-limited | May require EM_API_KEY that could be unconfigured. Subject to 3-indicator × 5-entity per-query limit. |
| 4th | **baidu-search / web_search** | Qualitative info: management strategy, regulatory penalties, news context | Use for context and qualitative analysis, not primary financial data. |

**Recommended query sequence (optimized for completeness + API efficiency):**

*Phase A — Core financials (Wind, 2-3 queries):*
- Query 1 (target bank): `get_stock_fundamentals` — 最近3年总资产、营业收入、归母净利润、ROE、ROA
- Query 2 (target bank): `get_stock_fundamentals` — 最近3年净息差NIM、不良贷款率、拨备覆盖率、资本充足率、核心一级资本充足率
- Query 3 (target bank): `get_stock_fundamentals` — 最近3年利息净收入、手续费及佣金净收入、成本收入比、每股净资产

*Phase B — Bank-specific deep data (ifind F9, 3-5 queries — CRITICAL for report depth):*
- Query 4: `"{bank}{code}最近3年对公贷款总额、零售贷款总额、贷款总额、存款总额"` → triggers F9 银行分析指标 table
- Query 5: `"{bank}{code}2024-2025年房地产行业贷款金额、房地产业不良率、个人住房贷款金额"` → RE exposure
- Query 6: `"{bank}{code}2023-2025年生息资产平均利率、付息负债平均利率、净利差"` → NIM decomposition
- Query 7: `"{bank}{code}最近5年送股、转增股本、配股、增发历史"` → capital events (CHECK for DPS retroactive adjustment needs)
- Query 8: `"{bank}{code}董事长、行长、高管团队名单"` → management info (F9 管理层 table)

*Phase C — Peer comparison (ifind batch query first, then Wind for补充):*
- **首选（ifind batch，1 query）：** `"{peer1}、{peer2}、{peer3}、{peer4}、{peer5} 2025年总资产、营业收入、归母净利润、ROE、净息差、不良贷款率、拨备覆盖率、资本充足率"` — 一次 query 同时触发所有银行的 F9 银行分析指标模块，返回各银行的全量指标（含成本收入比、存贷比、净利差、CET1等同业对比关键字段），远比逐家 Wind 查询高效且口径一致
- **补充（Wind，按需）：** 如需同业 3 年趋势数据或 Wind 特有字段（如每股净资产 BPS），再逐家 `get_stock_fundamentals` 查询

*Phase D — Regional economy (ifind EDB, 1-2 queries):*
- `"{province}2023-2025年GDP、GDP增速、人均可支配收入"`
- `"{city}2023-2025年GDP、GDP增速、人均GDP"` (for city/rural commercial banks)

**DPS source reconciliation (known pitfall):** Wind may return DPS as a single dividend payment (e.g., 0.28 元) while ifind F9 returns annual cumulative DPS including interim dividends (e.g., 0.66 元). ALWAYS cross-check with ifind F9 分红明细 table, which shows the full `股利支付率` and annual cash dividend total. For banks with stock splits/转增/可转债转股, also check if total share count changed — DPS must be compared on a consistent share base. 可转债转股的完整分析流程（调整后转股价计算、转股比例估算、DPS追溯调整方法）详见 `references/ifind_f9_bank_data_model.md`。

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

> 📖 **ifind F9 bank data reference**: For a complete map of ifind-repilot's F9 bank analysis modules (loan structure by industry with NPL, bank analysis indicators, deposit structure, dividend history, management bios, employee compensation), see `references/ifind_f9_bank_data_model.md`. This is the most valuable single data source for bank research — far richer than Wind or mx for bank-specific metrics.

### Step 4: Compile the Research Report

Use the report template at `references/report_template.md` as the standard structure. The template follows a business-logic-driven organization (modeled after top brokerage research reports) rather than a financial-statement-driven layout. Key structural principles:

- **Organize by business logic, not by accounting line items.** Each section should follow the chain: conclusion → data → reasoning.
- **Embed peer comparisons within each section** rather than concentrating them in a single chapter.
- **Section II (Regional Economy)** is essential for city/rural commercial banks; may be condensed for nationwide banks.
- **Section III (Business Analysis)** is the core section — analyze each business line (Corporate / Retail / Financial Markets) with depth on strategy, products, client segments, and competitive advantage.
- **Section V (Asset Quality)** must include dedicated subsections for real estate and LGFV exposure with full-caliber quantification (on-balance-sheet loans + bond investments + non-standard + off-balance-sheet), trend analysis, and stress scenarios.
- **Section VII (Risk Factors)** is a key section of the report — each risk must be specific, quantified, and assessed for trajectory. Include dedicated deep-dive subsections for real estate credit risk, LGFV/local government debt risk, consumer credit risk, and market risk. Conclude with a Risk Summary Matrix.

**Key writing principles:**

- **Writing format — narrative over bullet (critical user preference):** The user explicitly corrected that bullet points are too condensed — they assume prior knowledge and compress away the reasoning. When explaining strategic concepts, business model details, or analytical conclusions, use **narrative paragraphs**, not bullet lists. The pattern is: **conclusion-first** (state the key finding in one sentence), then **narrative expansion** (2-4 paragraphs explaining what it means, why it matters, and how it works in plain language). For example, instead of listing "零售转型" as "- 零售AUM增长 → 手续费占比提升 → 风险定价能力增强", write 3 paragraphs explaining what 零售转型 means for a bank (shift from corporate-driven to retail-driven revenue), how it changes the business model (from interest rate spread dependency to fee-based and wealth management income), and why it creates a competitive moat (customer stickiness, cross-sell potential, lower cyclical earnings volatility). Bullet lists are acceptable for raw data tables, financial metrics, and short factual enumerations — NOT for explaining concepts, strategies, or analytical reasoning.
- **Attribution discipline (critical):**
  - **Do not invent packaging phrases** that sound like research report quotes (e.g., "息差筑底", "资产质量向好拐点", "业绩触底回升"). Use plain descriptive language or quote the source directly with attribution.
  - **Do not reduce multi-factor causality to a single cause.** If a metric change (e.g., NIM narrowing) has multiple drivers (LPR cut + deposit cost rigidity + loan repricing + risk preference shift), list all factors. Never say "X导致Y" when the reality is "X、Y、Z多重因素共振".
  - **When using company/management language, mark it as such.** Distinguish between: company原话 ("息差有望企稳"), 媒体报道 ("净息差收窄压力加大"), and analyst包装 ("息差筑底"). Do not mix these without attribution.
  - **Data points require source tags.** Every quantitative claim (revenue, NIM, NPL ratio, ranking) must have an inline source note on first mention.

**Section completeness rule:** Each section of the report must have at least one data table with actual numbers. If P0 data for a section is missing, that section must still be written using qualitative analysis from news/reports, with explicit notation of what data is unavailable.

### Step 5: Report Completeness Check (MANDATORY)

Before delivering the report, run this self-check:

1. **Data table audit**: Count data tables in the report. A proper report should have at least 8-10 tables. If fewer, identify which sections are missing tables and fill them.
2. **Placeholder scan**: Search for `[...]` or empty cells in tables. Every placeholder must be either filled or explicitly marked as "未披露" with a reason.
3. **Section balance check**: Each major section (I-VIII) should have roughly comparable depth. If one section is 500 words and another is 50 words, the shorter one needs more work.
4. **Peer comparison check**: Are there at least 3 peer banks referenced in the report? Are comparisons embedded in relevant sections?
5. **Risk section check**: Does Section VII have quantified exposures? Is the Risk Summary Matrix complete?
6. **Data period consistency check**: For every peer comparison table, verify that period metrics (ROE, NIM, net profit growth) use consistent reporting periods. If the target bank uses annual data but peers use quarterly/interim data, each table cell must be labeled with its data source, and no ranking or qualitative conclusions (e.g., "leading", "second only to") should be drawn from mixed-period data.

If any check fails, fix the report before delivery. Do NOT deliver an incomplete report.

### Step 6: Deliver the Report

1. Generate clean Markdown content

**Output directory**: All output files are saved to the current workspace root directory. No subdirectories are created.

**File naming**:
- A-share bank reports: `[stock code]_[bank name]_深度研究报告_YYYYMMDD_HHmm.md` (e.g., `600015_华夏银行_深度研究报告_20260428_1430.md`)
- HKEX bank reports: `[stock code]_[bank name]_深度研究报告_YYYYMMDD_HHmm.md` (e.g., `01398_工商银行_深度研究报告_20260428_0915.md`)
- US-listed Chinese bank reports: `[ticker]_[bank name]_深度研究报告_YYYYMMDD_HHmm.md` (e.g., `ACH_宜人金科_深度研究报告_20260428_1100.md`)

> **Why HHmm suffix?** Adding hour-minute prevents same-day overwrites without requiring directory scans for version numbers. Two research sessions on the same bank won't share the same filename unless started within the same minute.

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
