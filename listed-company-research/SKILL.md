---
name: listed-company-research
description: "This skill should be used when the user wants to conduct in-depth fundamental research on any listed company, including A-share (China), Hong Kong-listed (HKEX), or US-listed (NYSE/NASDAQ) stocks. Triggers include phrases like '研究XX公司', '分析XX', 'XX公司基本面', '帮我看看XX', 'deep dive XX', 'research XX company', '管理层展望', '战略规划', 'management strategy', 'outlook', or when the user provides a stock code/ticker and asks for analysis. The skill produces a comprehensive, neutral-analysis Markdown report covering company overview, detailed business decomposition, industry competitive landscape, management outlook & strategy, and risk factors. It does NOT include financial statement modeling, valuation, stock ratings, or investment recommendations."
metadata:
  author: Mike Chen
  version: '4.0'
---

# Listed Company Fundamental Research

## Workflow

### Step 1: Preparations

#### Identify the Target Company

Parse the user's input to identify the target company:
- Extract company name, stock code, ticker, or any identifiable reference
- Determine the listing market based on the stock code format:
  - A-share: 6xxxxx (Shanghai), 0xxxxx (Shenzhen Main), 3xxxxx (ChiNext), 688xxx (STAR)
  - HKEX: 0xxxxx or 8xxxxx or 2xxxxx (usually with .HK suffix)
  - US: alphabetic ticker (e.g., AAPL, TSLA, BABA)
- If ambiguous, ask the user to clarify which company or market

#### Determine Data Freshness Context (PRE-QUERY — do this BEFORE any data query)

This is the #1 data freshness failure mode — querying "2024年年报" when "2025年年报" is already published.

- Use `terminal('date')` to determine the current calendar year and month.
- **A股 disclosure deadlines:** 年报 by 4/30, 一季报 by 4/30, 中报 by 8/31, 三季报 by 10/31.
- **Rule:** In month M of year N, determine the latest guaranteed-available report and query it FIRST. Example: June 2026 → latest annual = 2025年年报, latest quarter = 2026Q1.
- **Segment/product revenue:** Always from the LATEST annual report.
- **前十大股东:** Always from the LATEST quarterly report (not annual if a newer quarterly exists).
- **Industry/market data:** Search for the most recent available (within 6 months if possible).
- **Market data (股价/市值/PE):** Use real-time quotes.

These freshness rules apply throughout the entire workflow and are verified again in the Step 5 checklist.

### Step 2: Collect Information

Gather as much publicly available information as possible. **Always follow this priority order:**

**Priority 0:**

Start the annual report download in background (item 1), then immediately proceed to Google Sheets discovery (item 2) without waiting for the download to finish.

1. Multi-year annual report download (MANDATORY)
Download multi-year annual report PDFs (up to 10 years; for recently-listed companies, cover all available years). Annual reports are the primary source for: business segments / product-line revenue / subsidiary data, management MD&A (see `references/multi-year-mda-review.md`), risk factors, and financial footnotes (AR aging, provision tables, segment notes) that structured databases lack.
    - **A-share**: `cd ~/Projects/tinyant/cninfo && node index.js --codes <6-digit> --annual --year <start-end>`. See `references/cninfo-annual-report-extraction.md` for PDF download and MD&A text extraction.
    - **HKEX**: Use `~/Projects/tinyant/hkexnews/index.js` (港交所披露易), NOT cninfo. Example: `cd ~/Projects/tinyant/hkexnews && node index.js --codes 02858 --annual --year 2019-2022`.

2. Google Sheets historical financials (MANDATORY)
Check the user's Google Sheets for the target company's tab via the `productivity-tools` skill (formerly google-workspace). Always discover tabs via Drive API (see `references/gs-financial-structure.md`).
    - Tab found (e.g., `<公司名>财务`): extract multi-year trends for same-CIQ peer cross-validation (see reference for priority metrics & extraction pattern).
    - Tab NOT found: follow the fallback flow at the end of `references/gs-financial-structure.md` (record in §6.3 + fall back to mx).
    - Limitations: no segment/product detail (use annual reports); not a source for latest marginal data (quarterly updates, real-time quotes). **Pitfall:** skill was renamed from `google-workspace` to `productivity-tools`.

**Priority 1 — Installed Skills (check before web search):**

Scan all installed skills for relevance. Use them as primary data source; fall back to web search only when insufficient. Track every skill invoked for the "Sources & Limitations" section.

Determine mode based on information type:

**Mode A — Structured data points** (财务指标, 股东, 行情, 估值, 股本, 宏观数据):
Data availability is good; query ONE source in fallback order, move to next only on failure or rate-limit:
wind-mcp-skill → ifind-repilot-finance-data-search → mx-finance-data → baidu-search (last resort for qualitative context)
See `references/wind-mcp-query-patterns.md` for Wind query patterns.

**Mode B — News & information search** (公告, 研报, 新闻舆情, 政策, 行业动态):
Different sources have different coverage; query ALL of the following IN PARALLEL, then deduplicate and cross-validate:
- wind-mcp-skill (公司公告 RAG, exchange filings)
- ifind-repilot-news-search (全市场金融资讯, 新闻舆情)
- mx-finance-search (东方财富生态: 研报, 公告, 财经新闻)
- baidu-search (通用网络: 政策文件, 行业媒体, 非金融数据库覆盖的信息)；调用时 `count` 设为 50（拉满结果条数）。结果须过滤百家号/自媒体/虎嗅等非权威源，仅保留权威信源（雪球保留）

When a research task needs both modes (e.g., "查财务数据 + 最新公告"), run Mode A and Mode B concurrently.

**Priority 2 — Company Primary Sources** (when skills insufficient):
- 20-F / 6-K filing, prospectus, company website
- Earnings call transcripts — critical for management outlook (Chapter 4)
- Investor day / capital markets day presentations — key for strategic plans
- ESG/CSR reports

**Priority 3 — Regulatory & Exchange Sources:**
- Exchange announcements, regulatory filings (CSRC, SEC, SFC)

**Priority 4 — Third-Party Analysis:**
- Industry research reports, brokerage reports, reputable financial media

**Priority 5 — General Context:**
- Market commentary, peer disclosures, macroeconomic/policy context

**Collection rules (universal constraints):**

- Prioritize primary sources over secondary; prefer recent data over older data.
- When sources conflict, present both perspectives with attribution.
- **Table/list completeness**: Present ranked lists in full. Do NOT selectively skip entries.
- **Anti-hallucination (quantitative)**: Never fabricate data or use model knowledge to fill quantitative claims. If not found, note the gap explicitly.
- **Anti-hallucination (qualitative)**: Never infer specific company/client/supplier names from industry context. Only list names from sourced disclosures.
- **Estimation transparency**: When decomposing via residual method, label each component's confidence: directly measured > benchmarked > residual. Never present residual estimates with the same confidence as directly sourced data. See `references/derived-metrics-extraction.md`.

### Step 3: Analytical Patterns (load reference when triggered)

Before beginning data collection, scan the trigger table below and load every reference whose condition matches the target company.

| Trigger condition | Reference file |
|---|---|
| Business model transformation claims (SaaS转型, 轻资产转型, 平台化) | `revenue-quality-diagnostics.md` |
| Lending/financing/guarantee operations (助贷, 融资租赁, 消费金融, 担保) | `financial-institution-metrics.md` + `financial-company-asset-quality.md` + `financial-company-ecl-analysis.md` |
| Complex fee structures with derived metrics (净服务费率, take rate) | `derived-metrics-extraction.md` |
| Need to reverse-engineer a hidden denominator from disclosed ratio | `reverse-engineering-ratio-formulas.md` |
| Extreme YoY discontinuity (e.g., revenue -40%+ for profitable company) | `accounting-restatement-analysis.md` |
| User flags balance-sheet risk (应收账款, 合同资产, 减值) | `annual-report-financial-deep-dive.md` + `accounts-receivable-risk-analysis.md` |
| Recent M&A activity (acquired subsidiaries, terminated deals) | `ma-data-collection.md` |
| User asks about 牛散 background | `niusan-background-investigation.md` |
| Ownership change / new controlling shareholder | `new-controlling-shareholder-governance.md` |
| "增收不增利" (revenue up but profit down) | `profit-decomposition-framework.md` |
| Chemical/petrochemical, user asks for cycle analysis | `chemical-industry-cycle-analysis.md` |
| Pharma company, need product-line revenue breakdown | `pharma-annual-report-product-breakdown.md` |
| Need to extract MD&A text from annual report PDFs | `cninfo-annual-report-extraction.md` |
| User frames Company B against a previously-analyzed Company A | `cross-company-benchmark-comparison.md` |
| Structural headwinds that played out in other markets | `cross-market-comparison-framework.md` |
| Researching 3+ companies in one industry simultaneously | `multi-company-research-pattern.md` |
| Auto finance / loan facilitation platform | `global-auto-finance-market-comparison.md` |
| Post-delivery: company has physical products AND (user requests images OR products are highly visual) | `product-images-enhancement.md` |

**Inline patterns (short, high-frequency rules without dedicated references):**

**Equipment company leading indicators (合同负债 + 产销量):**
For hardware/equipment companies, two leading indicators: (1) 合同负债（预收账款）— growing = strong backlog; (2) 产销量库存表 — inventory build-up can signal demand ramp or overstocking, cross-check with subsequent quarters.

**A+H dual-listed companies:**
Always prefer A-share data as primary (CAS standards, more granular). Present A-share 前十大股东 for main ownership; note H-share holdings separately. Report both A (CNY) and H (HKD) valuations; note AH溢价.

**Pure HK-listed Chinese companies (非A+H):**
Currency pitfall: mx-finance-data returns summary in HKD but detailed statements in RMB — always check 原始币种. No "前十大股东" format; use HKEX "Substantial Shareholders" (5%+ threshold). HKEX MD&A less detailed; supplement with brokerage reports.

**DPS adjustment through stock splits (转增):**
When the company has had splits/bonus shares (e.g., 10转6), per-share metrics before and after are NOT comparable. Adjust retroactively or annotate. Dividend payout ratios (% of net profit) are unaffected.

**Quarterly YoY decline analysis (高基数 vs 需求恶化):**
When the latest quarter shows YoY decline, check three causes in order: (1) High-base from prior-year policy stimulus (e.g., 以旧换新 pulling demand forward); (2) Year-end delivery rush consuming Q1 orders; (3) Actual demand deterioration — only if neither (1) nor (2) applies. Diagnostic: compare 扣非 vs 归母 YoY — if gap is large, the decline is non-recurring items, not core operations.

### Step 4: Write the Research Report

Structure the report following `references/report-template.md` (five chapters: Company Overview → Business Deep Dive → Industry & Competitive Landscape → Management Outlook & Strategy → Risk Factors). Load that file for full section-by-section guidance.

**Writing principles (universal):**

- Plain language for an intelligent non-specialist. Chinese (中文) default unless user requests otherwise.
- Neutral, objective tone — facts and analysis, not opinions or recommendations.
- **Narrative over bullet (critical user preference):** Use narrative paragraphs for explaining strategies, business models, and analytical conclusions. Pattern: conclusion-first sentence → 2-4 paragraphs of expansion. Bullets acceptable only for raw data tables and short factual enumerations.
- **Attribution discipline:** Distinguish 公司原话 vs 媒体报道 vs analyst packaging. Do not invent framing phrases or reduce multi-factor causality to a single cause. Cite sources inline on first mention.
- **Perspective retention (materialist dialectical value):** Only retain perspectives that satisfy both criteria; discard all others. (1) Materialist nature: the perspective is grounded in a fact, not an opinion — predictions about future changes count as opinions. (2) Unity of opposites: if the perspective holds, it should drive iteration of the original conclusion toward a more comprehensive understanding, not merely serve as supplementary incremental information.

**Cross-market comparison (when applicable):** When the target faces structural headwinds with analogues in other markets, include a comparison appendix per `references/cross-market-comparison-framework.md`.

### Step 5: Review and Deliver

**Output filename:** `./[stock code]_[company name]_深度研究报告_YYYYMMDD_HHmm.md`

**Pre-delivery checklist (each item is a quality gate — fix before saving):**

1. **Appendix-to-body consistency**: If deep-dive appendices contradict main body characterizations, update ALL affected body sections and add inline cross-references.
2. **Data freshness audit**: Verify every data point against Step 1 freshness rules. Scan every `[Source: ...]` tag — replace any citation of a prior-year AR when a newer one exists.
3. **Restatement base-effect**: If 前期会计差错更正 occurred, present BOTH restated and pre-restated data; calculate both adjusted-base and original-base growth rates. (See `references/accounting-restatement-analysis.md`.)
4. **Completeness**: Stock codes/names accurate, each major section substantive, risk factors thorough.
5. Save the Markdown file and present to user.

## Living Document Workflow

After initial delivery, the user frequently asks follow-up questions. The workflow:

1. Answer the question with sourced research.
2. Append as Q&A to the report's appendix (`### E1.`, `### E2.`, etc.).
3. Re-save to the same file path.
4. Commit to the skills repo if any reference files were modified.

**Batch data collection before report updates**: When adding new data (e.g., ECL breakdown, sub-mode analysis), collect ALL available historical data across multiple years BEFORE patching the report. Do not update incrementally.
