---
name: listed-bank-research
description: This skill should be used when the user wants to conduct in-depth fundamental research on any listed bank in A-share (China) market, Hong Kong-listed (HKEX) bank stocks, or US-listed Chinese banks. Triggers include phrases like "研究XX银行", "分析XX银行基本面", "帮我看看XX银行", "XX银行深度分析", "研究银行股", "分析银行财报", or when the user provides a bank stock code/ticker and asks for analysis. The skill produces a comprehensive, neutral-analysis Markdown report covering bank business model, financial performance, asset quality, capital adequacy, profitability drivers, and risk factors. It does NOT include financial statement modeling, valuation, stock ratings, or investment recommendations.
metadata:
  author: Mike Chen
  version: '2.8'
---

# Listed Bank Research Skill

## Overview

This skill enables comprehensive fundamental research on listed banks (上市银行), producing detailed Markdown reports based on annual reports, interim reports, and quarterly disclosures. The research framework follows securities industry standards, covering business model analysis, financial statement decomposition, key performance indicators (KPIs), asset quality assessment, capital adequacy, and risk factor identification.

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

Refer to `references/bank_analysis_framework.md` for the complete analytical framework (business model logic, financial statement decomposition, NIM drivers, asset quality indicators, capital management, liquidity risk, peer comparison, banking sector classification). Identify what information needs to be obtained.

### Step 3: Systematic Data Collection (CRITICAL — Do NOT skip)

**This is the most important step.** The #1 cause of uneven report quality is ad-hoc, unstructured data collection. You MUST follow this systematic approach.

#### 3.1 Data Collection Checklist

Use the fillable tracking template at `references/data_collection_checklist.md` (Tier 0-5, covering MD&A review, core financials, business structure, asset quality detail, capital & liquidity, and peer comparison). Each item is marked [P0] (must have), [P1] (should have), or [P2] (nice to have).

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
| 1st | **ifind-repilot-finance-data-search** (同花顺 F9 银行模块) | Bank-specific deep data: loan structure by industry with NPL rates, migration rates, capital composition, deposit structure, dividend history, employee/management details, NIM decomposition | The F9 outputs `F9-{bank}-银行贷款结构`, `F9-{bank}-银行分析指标`, `F9-{bank}-银行存款结构` are GOLD MINES — far richer than any other source. See `references/ifind_f9_bank_data_model.md` for the full query map. |
| 2nd | **Wind MCP** (`get_stock_fundamentals`) | Core 3-year financials (总资产/营收/净利润/ROE/NIM/不良率/拨备覆盖率/CAR/CET1), peer comparison, basic info, shareholders, events | Good for multi-year trends and peer side-by-side. BUT: often returns `None` for bank-specific metrics (ROA, RE exposure, fee breakdown). Unit inconsistency warning: total assets/deposits may come back in "万亿元" while loans come back in "亿元" — ALWAYS verify units. |
| 3rd | **mx-finance-data** (东方财富) | Fallback if Wind/ifind unavailable or rate-limited | May require EM_API_KEY. Subject to 3-indicator × 5-entity per-query limit. |
| 4th | **baidu-search / web_search** | Qualitative info: management strategy, regulatory penalties, news context | Use for context and qualitative analysis, not primary financial data. |

**Recommended query sequence (optimized for completeness + API efficiency):**

*Phase A — Core financials (Wind, 2-3 queries):*
- Query 1: `get_stock_fundamentals` — 最近3年总资产、营业收入、归母净利润、ROE、ROA
- Query 2: `get_stock_fundamentals` — 最近3年净息差NIM、不良贷款率、拨备覆盖率、资本充足率、核心一级资本充足率
- Query 3: `get_stock_fundamentals` — 最近3年利息净收入、手续费及佣金净收入、成本收入比、每股净资产

*Phase B — Bank-specific deep data (ifind F9, 3-5 queries — CRITICAL for report depth):*
- Query 4: `"{bank}{code}最近3年对公贷款总额、零售贷款总额、贷款总额、存款总额"` → F9 银行分析指标
- Query 5: `"{bank}{code}2024-2025年房地产行业贷款金额、房地产业不良率、个人住房贷款金额"` → RE exposure
- Query 6: `"{bank}{code}2023-2025年生息资产平均利率、付息负债平均利率、净利差"` → NIM decomposition
- Query 7: `"{bank}{code}最近5年送股、转增股本、配股、增发历史"` → capital events (CHECK for DPS retroactive adjustment needs)
- Query 8: `"{bank}{code}董事长、行长、高管团队名单"` → F9 管理层

*Phase C — Peer comparison (ifind batch first, then Wind for补充):*
- **首选（ifind batch，1 query）：** `"{peer1}、{peer2}、{peer3}、{peer4}、{peer5} 2025年总资产、营业收入、归母净利润、ROE、净息差、不良贷款率、拨备覆盖率、资本充足率"` — 一次 query 同时触发所有银行的 F9 银行分析指标模块，远比逐家 Wind 查询高效且口径一致
- **补充（Wind，按需）：** 如需同业 3 年趋势或 Wind 特有字段（如 BPS），再逐家 `get_stock_fundamentals`

*Phase D — Regional economy (ifind EDB, 1-2 queries):*
- `"{province}2023-2025年GDP、GDP增速、人均可支配收入"`
- `"{city}2023-2025年GDP、GDP增速、人均GDP"` (for city/rural commercial banks)

**DPS source reconciliation (known pitfall):** Wind may return DPS as a single dividend payment while ifind F9 returns annual cumulative DPS including interim dividends. ALWAYS cross-check with ifind F9 分红明细 table. For banks with stock splits/转增/可转债转股, DPS must be compared on a consistent share base — 可转债转股的完整分析流程详见 `references/ifind_f9_bank_data_model.md`。

#### 3.3 Data Gap Handling Protocol

When a data field cannot be obtained after reasonable effort:

**Rule 1 — Never fabricate data.** If a number is not available, say so explicitly.

**Rule 2 — Tier-based handling:**
- **P0 data missing**: STOP. Attempt alternative sources (web_search, direct PDF extraction). If still unavailable, note: "【数据缺口】该指标为必需数据，但未能从公开渠道获取。"
- **P1 data missing**: Make 2 attempts from different sources. If still unavailable, write: "【数据未披露】XX银行未在年报中披露该指标" and proceed.
- **P2 data missing**: Omit gracefully. Do not include empty placeholders.

**Rule 3 — Use proxy data when direct data is unavailable:**
- If loan industry breakdown not disclosed → use broker research reports for estimates
- If LCR/NSFR not available for HKEX-listed banks → note that HKMA reporting requirements differ from mainland
- If credit cost not directly stated → calculate as: 信用减值损失 / 平均贷款余额

**Rule 4 — Cross-validation**: When data from different sources conflicts, prefer the annual report as the source of truth. Note discrepancies.

### Step 4: Compile the Research Report

Use the report template at `references/report_template.md` as the standard structure. Key structural principles:

- **Organize by business logic, not by accounting line items.** Each section follows: conclusion → data → reasoning.
- **Embed peer comparisons within each section** rather than concentrating them in a single chapter.
- **Section II (Regional Economy)** is essential for city/rural commercial banks; may be condensed for nationwide banks.
- **Section III (Business Analysis)** is the core — analyze each business line (Corporate / Retail / Financial Markets) with depth on strategy, products, client segments, and competitive advantage.
- **Section V (Asset Quality)** must include dedicated subsections for real estate and LGFV exposure with full-caliber quantification (on-balance-sheet loans + bond investments + non-standard + off-balance-sheet), trend analysis, and stress scenarios.
- **Section VII (Risk Factors)** — each risk must be specific, quantified, and assessed for trajectory. Include dedicated deep-dives for real estate credit risk, LGFV/local government debt risk, consumer credit risk, and market risk. Conclude with a Risk Summary Matrix.

**Key writing principles:**

- **Narrative over bullet (critical user preference):** When explaining strategic concepts, business model details, or analytical conclusions, use **narrative paragraphs** — conclusion-first (one sentence), then 2-4 paragraphs explaining what/why/how. Bullets are for data tables, metrics, and short factual enumerations only.
- **Attribution discipline (critical):**
  - **Do not invent packaging phrases** (e.g., "息差筑底", "资产质量向好拐点"). Use plain descriptive language or quote the source directly with attribution.
  - **Do not reduce multi-factor causality to a single cause.** If a metric change has multiple drivers, list all factors. Never say "X导致Y" when the reality is "X、Y、Z多重因素共振".
  - **Distinguish company原话 ("息差有望企稳") vs 媒体报道 ("净息差收窄压力加大") vs analyst包装 ("息差筑底").** Do not mix without attribution.
  - **Data points require source tags** on first mention.
- **Perspective retention (materialist dialectical value):** Retain perspectives that hold materialist dialectical value, and discard all other perspectives. A perspective with materialist dialectical value must satisfy the following:
  - Materialist Nature: The subject of the perspective is a certain fact, rather than a certain opinion. Predictions about changes in facts are also considered opinions.
  - Unity of Opposites: If the perspective holds, it should serve as a driving force to further iterate the original conclusion, enabling a more comprehensive exploration of the matter, rather than merely serving as supplementary incremental information.

**Section completeness rule:** Each section must have at least one data table with actual numbers. If P0 data for a section is missing, write qualitative analysis from news/reports with explicit notation of what data is unavailable.

### Step 5: Report Completeness Check (MANDATORY)

Before delivering, run this self-check:

1. **Data table audit**: ≥8-10 tables. If fewer, fill missing sections.
2. **Placeholder scan**: No `[...]` or empty cells. Every placeholder filled or marked "未披露" with reason.
3. **Section balance check**: Sections I-VIII should have roughly comparable depth.
4. **Peer comparison check**: ≥3 peer banks referenced, comparisons embedded in relevant sections.
5. **Risk section check**: Section VII has quantified exposures, Risk Summary Matrix complete.
6. **Data period consistency check**: Peer comparison tables must use consistent reporting periods. Mixed-period data → label each cell's source, no ranking or qualitative conclusions.

If any check fails, fix before delivery.

### Step 6: Deliver the Report

1. Generate clean Markdown content.

**Output directory**: Current workspace root directory. No subdirectories.

**File naming**: `[stock code]_[bank name]_深度研究报告_YYYYMMDD_HHmm.md`
- A-share: `600015_华夏银行_深度研究报告_20260428_1430.md`
- HKEX: `01398_工商银行_深度研究报告_20260428_0915.md`
- US-listed: `ACH_宜人金科_深度研究报告_20260428_1100.md`

> **Why HHmm?** Prevents same-day overwrites without directory scans for version numbers.

2. Include data tables and cite data sources.
3. At the end of the report, include a **Data Completeness Summary** per the template in `references/report_template.md` (Appendix D).
