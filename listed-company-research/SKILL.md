---
name: listed-company-research
description: "This skill should be used when the user wants to conduct in-depth fundamental research on any listed company, including A-share (China), Hong Kong-listed (HKEX), or US-listed (NYSE/NASDAQ) stocks. Triggers include phrases like '研究XX公司', '分析XX', 'XX公司基本面', '帮我看看XX', 'deep dive XX', 'research XX company', '管理层展望', '战略规划', 'management strategy', 'outlook', or when the user provides a stock code/ticker and asks for analysis. The skill produces a comprehensive, neutral-analysis Markdown report covering company overview, detailed business decomposition, industry competitive landscape, management outlook & strategy, and risk factors. It does NOT include financial statement modeling, valuation, stock ratings, or investment recommendations."
metadata:
  author: Mike Chen
  version: '3.0'
---

# Listed Company Fundamental Research

## Workflow

### Step 1: Preparations

#### Identify the Target Company:

Parse the user's input to identify the target company:
- Extract company name, stock code, ticker, or any identifiable reference
- Determine the listing market based on the stock code format:
  - A-share: 6xxxxx (Shanghai), 0xxxxx (Shenzhen Main), 3xxxxx (ChiNext), 688xxx (STAR)
  - HKEX: 0xxxxx or 8xxxxx or 2xxxxx (usually with .HK suffix)
  - US: alphabetic ticker (e.g., AAPL, TSLA, BABA)
- If ambiguous, ask the user to clarify which company or market

#### Determine Current Fiscal Year Context (PRE-QUERY)

**Before issuing any data queries, determine which fiscal periods are available.** This is the #1 data freshness failure mode — querying "2024年年报" when "2025年年报" is already published.

- Use `terminal('date')` or check today's date to determine the current calendar year and month.
- **A股 annual report filing deadline:** 4月30日 (previous year's annual report). So by June of year N, year N-1's annual report is always available.
- **A股 quarterly reports:** 一季报 (by 4/30), 中报 (by 8/31), 三季报 (by 10/31).
- **Rule of thumb:** In June 2026 → latest annual report = 2025年年报, latest quarter = 2026年一季报. Always query these FIRST.
- **For segment/product revenue:** Always use the latest annual report (e.g., 2025年年报 in mid-2026), NOT the prior year's.

### Step 2: Collect Information

Gather as much publicly available information as possible. **Always follow this priority order:**

**Priority 0 — Multi-year annual report download (MANDATORY for all companies):**

MUST download multi-year annual report PDFs (up to 10 years; for recently-listed companies, cover all available years). Annual reports are the primary data source for: §2 business segments / product-line revenue / subsidiary operating data, §4.1/4.2/4.3 management MD&A (see `references/multi-year-mda-review.md` for the systematic multi-year extraction workflow), §5 risk factors, and financial footnote detail (AR aging, provision tables, segment notes, etc.) that structured databases do not provide.

- **A-share**: `node ~/Projects/tinyant/cninfo/index.js --codes <6-digit> --annual --year <start-end>`. The `--annual` flag is REQUIRED — without it the tool defaults to recent announcements only. See `references/cninfo-annual-report-extraction.md` for PDF download details and MD&A text extraction workflow.
- **HKEX**: Use `~/Projects/tinyant/hkexnews/index.js` (港交所披露易), NOT cninfo. Example: `node ~/Projects/tinyant/hkexnews/index.js --codes 02858 --year 2019-2022`.

**Priority 1 — Installed Skills (check before anything else):**
Before initiating any web search, scan all currently installed skills for any relevant to this research task. Use installed skills as the primary data source; only fall back to web search when they cannot provide sufficient data. Track every skill invoked (name, data provided, report section) for the "Sources & Limitations" section.

**Key installed skills (in query order):**

1. **google-workspace/productivity-tools** — CIQ-sourced Google Sheets (check FIRST for financial data): 19+ years of financial history (Key Stats, IS, BS, CF, Ratios). Primary source for multi-year trends, balance sheet detail, and cash flow breakdown. If the target company's tab is not found in known sheets, run the Drive API discovery workflow (see `references/gs-financial-structure.md`) before concluding the company is not tracked. **Limitation:** Google Sheets does NOT contain CIQ's `Industry Specific`, `Segments`, `Pension OPEB`, or `Supplemental` sheets — for those, use annual report appendices or credit rating reports. **⚠️ Pitfall — skill name mismatch:** This skill was renamed from `google-workspace` to `productivity-tools`. If you cannot find `google-workspace` in the skills list, load `productivity-tools` instead and use its Google Sheets functionality.

2. **wind-mcp-skill** (A-share/HK/US): Structured market data and real-time quotes — 前十大股东, 行情快照, 财务数据, 公司公告 (announcements RAG), 股本结构. Use for data NOT in Google Sheets (shareholder data, real-time prices, announcements) or when the target company is not tracked in any Google Sheet. See `references/wind-mcp-query-patterns.md` for proven query patterns.

3. **ifind-repilot** series (announcement-search / finance-data-search): Primary fallback for Wind/Google Sheets gaps — high-quality announcement retrieval and financial data. Use especially when mx-finance-data is rate-limited or returns unreliable data (e.g., DPS for HK stocks).
4. **mx-finance-data / mx-finance-search**: 东方财富 structured data and news/announcements. Supplement for real-time quotes, consensus estimates, and news search.

**Pitfall — "Residual method" for fee decomposition:** When decomposing a bundled fee into components, the residual is the **least reliable** estimate. Be explicit: "X% is directly from annual report, Y% is industry benchmark, Z% is residual and therefore uncertain." Never present residual estimates with the same confidence as directly sourced data. See `references/derived-metrics-extraction.md` for the full fee decomposition methodology.

**Workflow — IMA notes synchronization:** Update the local .md file first. Only push to IMA notes after the user says "finalize" or explicitly asks to sync.

**Priority 2 — Company Primary Sources** (when skills insufficient):
- 20-F / 6-K filing, prospectus, company website
- **Earnings call transcripts** — critical for management outlook (Chapter 4)
- **Investor day / capital markets day presentations** — key for strategic plans (Chapter 4)
- ESG/CSR reports

**Priority 3 — Regulatory & Exchange Sources:**
- Exchange announcements, regulatory filings (CSRC, SEC, SFC)

**Priority 4 — Third-Party Analysis:**
- Industry research reports, brokerage reports, reputable financial media, industry associations

**Priority 5 — General Context:**
- Market commentary, peer company disclosures, macroeconomic/policy context

**Collection rules:**
- Conduct multiple rounds of targeted searches to cover all aspects
- Prioritize primary sources over secondary; prefer recent data over older data
- When sources conflict, present both perspectives with attribution
- **Data freshness — industry data**: Always search for the most recent industry/market data available (within 6 months if possible), even if the company's latest filing is from a prior quarter.
- **Data freshness — shareholder info**: 前十大股东 MUST come from the LATEST quarterly report (not the annual report if a newer quarterly exists).
- **Data freshness — product revenue breakdowns**: Segment/product-level data MUST come from the LATEST annual report. If not disclosed, search recent 券商研报.
- **Table/list completeness**: When presenting ranked lists or TOP-N tables, present the complete list. Do NOT selectively skip entries.
- **Anti-hallucination (quantitative)**: Never fabricate data or use model knowledge to fill quantitative claims. If a figure is not found, note the gap explicitly. Model knowledge may only be used for qualitative context.
- **Anti-hallucination (qualitative)**: Never infer specific company names, client names, supplier names, or business relationships from industry context. Only list names that appear in sourced disclosures. If not disclosed, write "specific client names not disclosed."
- **Estimation methodology transparency (剩余法/残差法)**: When decomposing a total figure via residual method, label each component's confidence: directly measured > estimated via benchmarking > residual/remainder. Never present residual with same confidence as directly sourced data.

### Step 3: Analytical Patterns (load reference when triggered)

The following patterns guide specific analysis situations. Each has a dedicated reference file — load it via `skill_view(name="listed-company-research", file_path="references/...")` when the trigger condition applies.

**Load trigger table:**

| Trigger condition | Reference file | What it covers |
|---|---|---|
| Company claims business model transformation (SaaS转型, 轻资产转型, 科技赋能, 平台化) | `revenue-quality-diagnostics.md` | Fee rate benchmarking, cost structure consistency, revenue migration pattern, sub-mode analysis, business reclassification detection, overseas expansion claims verification |
| Company has lending/financing/guarantee operations (助贷, 融资租赁, 消费金融, 担保) | `financial-institution-metrics.md` + `financial-company-asset-quality.md` + `financial-company-ecl-analysis.md` | NIM, NPL, provision coverage, ECL three-stage, leverage, funding source analysis, off-balance-sheet guarantees, credit rating reports, 信用减值亏损 extraction |
| Company has complex fee structures with derived metrics (净服务费率, 净息差, take rate) | `derived-metrics-extraction.md` | How to find management-defined metrics in MD&A, extract calculation formulas from footnotes, build multi-year trends, decompose via residual method |
| Need to reverse-engineer a hidden denominator from disclosed ratio formula | `reverse-engineering-ratio-formulas.md` | Back-calculate undisclosed metrics from disclosed components |
| Wind shows extreme YoY discontinuity (e.g., revenue -40%+ for profitable company) | `accounting-restatement-analysis.md` | Detect 前期会计差错更正, build restated-vs-pre-restated comparison, flag base-effect distortion. **五粮液 2025 worked example.** |
| User flags specific balance-sheet risk (应收账款太大, 合同资产, 减值) | `annual-report-financial-deep-dive.md` + `accounts-receivable-risk-analysis.md` | AR aging tables, provision adequacy, top obligors, "两金" ratio, aging migration pressure, worked example (中材国际 2025) |
| Company is in equipment/manufacturing with physical products | (inline below) | 合同负债 + 产销量 as leading indicators |
| Company has recent M&A activity (acquired subsidiaries, terminated deals) | `ma-data-collection.md` | Query each entity separately, map acquisition timeline, flag goodwill/integration risks |
| User asks about 牛散 (natural person shareholder) background | `niusan-background-investigation.md` | Search patterns, known cases (张弦、沈奇迪、葛卫东等), pitfalls |
| Ownership change / new controlling shareholder | `new-controlling-shareholder-governance.md` | Governance track record, past integration cases, pros/cons assessment |
| "增收不增利" (revenue up but profit down) | `profit-decomposition-framework.md` | Decompose gross margin changes, identify structural drivers, cross-validate management narratives |
| Chemical/petrochemical company, user asks for cycle analysis | `chemical-industry-cycle-analysis.md` | PPI/CIP/capex bottom signals, MDI cycle pattern, supply-demand, oil price impact |
| Pharma company, need product-line revenue breakdown | `pharma-annual-report-product-breakdown.md` | 主营构成→核心产品毛利率表→子公司经营数据, extraction via cninfo PDF or ifind-repilot RAG |
| Need to extract MD&A text from annual report PDFs (single-year mechanics) | `cninfo-annual-report-extraction.md` | TOC-vs-content pitfall, section number variation, end-marker detection, keyword filtering |
| User frames Company B against a previously-analyzed Company A's success pattern ("顺着XX的经验看看YY") | `cross-company-benchmark-comparison.md` | Trajectory verification (organic second-curve vs U-shape reversion), margin spread test, business model compatibility. 宇通 vs 中材国际 case study. |
| Target faces structural headwinds that played out in other markets | `cross-market-comparison-framework.md` | Cross-market comparison: (Type 1) consumer goods/demographic decline → Japan/Korea precedents; (Type 2) fintech → US/UK precedents |
| Researching 3+ companies in one industry simultaneously | `multi-company-research-pattern.md` | Parallel subagent workflow, cross-reference phase, industry synthesis |
| Auto finance / loan facilitation platform company | `global-auto-finance-market-comparison.md` | Market size, penetration rates, interest rates, key players across USA/Europe/Japan/Australia/China |

**⚠️ Trigger table enforcement:** The table above is NOT a "load when convenient" list. Before beginning Step 2 data collection, scan every row and load every reference whose trigger condition matches. 

**Inline pattern — Equipment company leading indicators (合同负债 + 产销量):**
For hardware/equipment companies (医疗器械、工业设备、消费电子等), two metrics are powerful leading indicators:
1. **合同负债（预收账款）**: Advance payments from customers. Growing = strong backlog. Query via Wind: `688271.SH2025年年报合同负债预收账款`.
2. **产销量库存表**: Annual report production/sales/inventory table. Inventory build-up in a product line can signal demand ramp or overstocking — cross-check with subsequent quarters.

**Inline pattern — A+H dual-listed companies:**
- **Always prefer A-share data** as primary source (CAS standards, more granular). H-share uses IFRS/HKFRS.
- Present A-share 前十大股东 for main ownership; note H-share holdings separately.
- Report both A (CNY) and H (HKD) valuations; note AH溢价.

**Inline pattern — Pure HK-listed Chinese companies (非A+H):**
- **Currency pitfall**: mx-finance-data returns summary metrics in HKD but detailed statements in RMB. Always check "原始币种" before mixing sources.
- No "前十大股东" format. Use HKEX "Substantial Shareholders" (5%+ threshold).
- HKEX MD&A less detailed than A-share. Supplement with brokerage reports.

**Inline pattern — Accounting restatement (前期会计差错更正) detection:**
When Wind shows extreme YoY change (e.g., revenue -54% for a profitable company with no operational catastrophe), IMMEDIATELY search for restatement announcements. This is a critical data-quality gate. See `references/accounting-restatement-analysis.md` for the full detection workflow and 五粮液 2025 worked example.

**Inline pattern — DPS adjustment through stock splits (转增):**
When the company has had stock splits/bonus shares (e.g., 10转6), per-share metrics from before and after are NOT comparable. Either adjust historical figures retroactively or clearly annotate. Dividend payout ratios (% of net profit) are unaffected.

**Inline pattern — Quarterly YoY decline analysis (高基数效应 vs 需求恶化):**
When the latest quarter shows YoY revenue/profit decline (e.g., Q1 -8% / -13%), do NOT immediately conclude demand deterioration. Check three causes in order:
1. **High-base effect from policy-driven demand surge:** Did the prior-year same quarter benefit from a one-time policy stimulus (e.g., "以旧换新" subsidy pulling demand forward)? If so, the decline is a base-effect artifact, not operational deterioration.
2. **Year-end delivery rush pulling orders forward:** Did the prior December show an abnormally high monthly peak (e.g., 2x-3x of normal months)? If so, Q1 orders were partially consumed in Q4, creating a seasonal trough.
3. **Actual demand deterioration:** Only if neither (1) nor (2) applies, investigate fundamental demand weakness.
**Diagnostic:** Compare扣非净利润 YoY vs 归母净利润 YoY — if 扣非 decline is much smaller (e.g., -5% vs -13%), the gap is non-recurring items, not core operations. Also check if subsequent months (e.g., April) recover to positive growth, confirming the decline was seasonal/base-effect.

### Step 4: Write the Research Report

Structure the report following the template in `references/report-template.md`. Load this file for the full template with detailed section-by-section guidance.

**Key writing principles:**
- Use simple, plain language — explain to an intelligent non-specialist
- For each business segment: what it is, who are the customers, what inputs are needed, how revenue is generated, business flow
- Support all claims with specific data points and cite sources
- Neutral, objective tone — present facts and analysis, not opinions or recommendations
- Use Chinese (中文) as the default output language unless the user requests otherwise
- **Writing format — narrative over bullet (critical user preference):** The user explicitly corrected that bullet points are too condensed. When explaining strategic concepts, business model details, or analytical conclusions, use **narrative paragraphs**. The pattern is: **conclusion-first** (state the key finding in one sentence), then **narrative expansion** (2-4 paragraphs). Bullets are acceptable for raw data tables, financial metrics, and short factual enumerations — NOT for explaining concepts, strategies, or analytical reasoning.
- **Attribution discipline (critical):**
  - **Do not invent packaging phrases** that sound like research report quotes (e.g., "销售真空期"). Use plain language or quote the source directly.
  - **Do not reduce multi-factor causality to a single cause.** Never say "X导致Y" when reality is "X、Y、Z多重因素共振".
  - **Distinguish company原话 vs 媒体报道 vs analyst包装.** Do not mix without attribution.
  - **Every quantitative claim must have an inline source note** on first mention.

**Cross-market comparison (recommended when applicable):**
When the target faces structural headwinds that have played out in analogous markets, proactively include a cross-market comparison as an appendix (e.g., Chinese consumer companies → Japanese peers; fintech → US/UK precedents). See `references/cross-market-comparison-framework.md`.

### Step 5: Review and Deliver

**Output**: `./[stock code/ticker]_[company name]_深度研究报告_YYYYMMDD_HHmm.md`

**MANDATORY — Pre-delivery Checklist:**

Run through this checklist before delivery. Each item is a quality gate that, if failed, must be fixed before saving:

1. **Appendix-to-body consistency**: If deep-dive appendices contradict the main body's characterizations (e.g., appendix reveals "light-asset" is actually heavy-risk), update ALL affected body sections (§2 business descriptions, §3.4 moat, §4 management narrative, §5 risk factors) AND add inline cross-references to the appendix.
2. **Data freshness audit**: Every data point verified against the most recent available source. (a) Financial metrics cite the latest annual report, with quarterly updates inline if available. (b) 前十大股东 from the latest quarterly (not annual report). (c) 子公司数据 from latest AR's "主要控股参股公司分析". (d) Market data (股价/市值/PE) uses real-time data. (e) Scan every `[Source: ...]` tag — replace any citation of a prior-year AR when a newer one is available.
3. **Restatement base-effect check**: If the company had a 前期会计差错更正, present BOTH restated and pre-restated data. Post-restatement YoY comparisons are distorted by artificially lowered bases — always calculate both adjusted-base and pre-restatement-base growth rates.
4. **Stock codes and company names** accurate. Each major section has substantive content. Risk factors section is thorough.
5. Save the Markdown file to the current workspace root directory and present it to the user.

### Step 6: Multi-Company Parallel Research (When Applicable)

When the user asks to research 3+ companies simultaneously, use the parallel subagent workflow in `references/multi-company-research-pattern.md`. **Critical**: After parallel Phase 1, always do a cross-reference Phase 2 before synthesizing the industry report in Phase 3.

### Step 7: Optional — Product Image Enhancement

For companies with physical products, optionally enhance Section 2 with product images after the main report is complete. See `references/product-images-enhancement.md`. Skip unless requested or products are highly visual.

## Research Framework

The report covers five core pillars:

1. **Company Overview** — Basic profile, listing info, market position summary
2. **Business Deep Dive** — Detailed breakdown of each business segment
3. **Industry & Competitive Landscape** — Industry overview, value chain, competitive dynamics
4. **Management Outlook & Strategy** — Management's view, strategic plans, track record credibility
5. **Risk Factors** — Business, financial, industry, regulatory, and other risks

**Explicitly excluded:** Financial statement modeling, valuation, stock ratings, target prices, investment recommendations.

## Living Document Workflow

After the initial report is delivered, the user frequently asks follow-up questions (e.g., "2018年亏损原因？", "新股东治理记录如何？"). The workflow:

1. **Answer the question** with sourced research.
2. **Append as Q&A** to the report's appendix section (typically `附录E` or create one). Use `### E1.`, `### E2.`, etc. numbering.
3. **Re-save the report** to the same file path.
4. **Re-upload to IMA notes** if the user requests (see `ima-skill`).
5. **Commit to the skills repo** if any reference files or the skill itself was modified.

Each Q&A entry should be self-contained and cite sources inline. The report is a living document that evolves across sessions.

**Workflow preference — batch data collection before report updates**: When adding new data (e.g., "add ECL breakdown", "add sub-mode analysis"), first collect ALL available historical data across multiple years/annual reports BEFORE making patches to the report. Do not update incrementally. This is especially important for: ECL breakdown tables (3-5 years), sub-mode splits, financial metrics (NIM, leverage, ROA/ROE trends).
