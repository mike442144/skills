---
name: listed-company-research
description: "This skill should be used when the user wants to conduct in-depth fundamental research on any listed company, including A-share (China), Hong Kong-listed (HKEX), or US-listed (NYSE/NASDAQ) stocks. Triggers include phrases like '研究XX公司', '分析XX', 'XX公司基本面', '帮我看看XX', 'deep dive XX', 'research XX company', '管理层展望', '战略规划', 'management strategy', 'outlook', or when the user provides a stock code/ticker and asks for analysis. The skill produces a comprehensive, neutral-analysis Markdown report covering company overview, detailed business decomposition, industry competitive landscape, management outlook & strategy, and risk factors. It does NOT include financial statement modeling, valuation, stock ratings, or investment recommendations."
metadata:
  author: Mike Chen
  version: '1.14'
---

# Listed Company Fundamental Research

## Workflow

### Step 1: Identify the Target Company

Parse the user's input to identify the target company:
- Extract company name, stock code, ticker, or any identifiable reference
- Determine the listing market based on the stock code format:
  - A-share: 6xxxxx (Shanghai), 0xxxxx (Shenzhen Main), 3xxxxx (ChiNext), 688xxx (STAR)
  - HKEX: 0xxxxx or 8xxxxx or 2xxxxx (usually with .HK suffix)
  - US: alphabetic ticker (e.g., AAPL, TSLA, BABA)
- If ambiguous, ask the user to clarify which company or market

### Step 1.5: Determine Current Fiscal Year Context (PRE-QUERY)

**Before issuing any data queries, determine which fiscal periods are available.** This is the #1 data freshness failure mode — querying "2024年年报" when "2025年年报" is already published.

- Use `terminal('date')` or check today's date to determine the current calendar year and month.
- **A股 annual report filing deadline:** 4月30日 (previous year's annual report). So by June of year N, year N-1's annual report is always available.
- **A股 quarterly reports:** 一季报 (by 4/30), 中报 (by 8/31), 三季报 (by 10/31).
- **Rule of thumb:** In June 2026 → latest annual report = 2025年年报, latest quarter = 2026年一季报. Always query these FIRST.
- **For segment/product revenue:** Always use the latest annual report (e.g., 2025年年报 in mid-2026), NOT the prior year's.

### Step 2: Collect Information

Gather as much publicly available information as possible. **Always follow this priority order:**

**Priority 0 — Installed Skills (check first before anything else):**
Before initiating any web search, scan all currently installed skills for any relevant to this research task (financial databases, document parsers, browser automation, etc.). Use installed skills as the primary data source; only fall back to web search when they cannot provide sufficient data. Track every skill invoked (name, data provided, report section) for the "Sources & Limitations" section.

**Key installed skills for listed company research (in query order):**

1. **google-workspace** — CIQ-sourced Google Sheets (check FIRST for financial data): The user maintains industry-specific Google Sheets with 19+ years of financial history (Key Stats, IS, BS, CF, Ratios). Use this as the primary source for multi-year financial trends, balance sheet detail, and cash flow breakdown — far deeper history than Wind/mx free tiers (3-year limit). If the target company's tab is not found in known sheets, run the Drive API discovery workflow (see `references/gs-financial-structure.md`) before concluding the company is not tracked. **Limitation:** Google Sheets does NOT contain CIQ's `Industry Specific`, `Segments`, `Pension OPEB`, or `Supplemental` sheets.

2. **wind-mcp-skill** (A-share/HK/US): Structured market data and real-time quotes — 前十大股东 (equity holders by quarter), 行情快照 (price/market cap/PE/PB), 财务数据 (fundamentals by quarter/year), 公司公告 (announcements RAG), 股本结构 (total shares, float, restricted). Use `stock_data` server for A-shares, `global_stock_data` for HK/US. See `references/wind-mcp-query-patterns.md` for proven query patterns. Use Wind for data NOT in Google Sheets (e.g., shareholder data, real-time prices, announcements) or when the target company is not tracked in any Google Sheet.

3. **mx-finance-data / mx-finance-search**: 东方财富 structured data and news/announcements. Good supplement; see memory for skill routing rules.
4. **ifind-repilot** series: High-quality fallback when mx-finance-data is rate-limited or returns unreliable data (especially DPS for HK stocks).

**Pitfall — A-share annual report downloads (cninfo):** The cninfo downloader (`~/Projects/tinyant/cninfo/index.js`) requires the `--annual` flag for annual reports. Without it, the tool defaults to recent-announcements mode and ignores `--year`. Correct: `node ~/Projects/tinyant/cninfo/index.js --codes 600066 --annual --year 2015-2024`. See `references/cninfo-annual-report-extraction.md` for the full workflow including MD&A section extraction from PDFs (TOC-vs-content pitfall, section number variation across years, end-marker detection).

**Pitfall — HK stock annual report downloads:** For HK-listed companies, use `~/Projects/tinyant/hkexnews/index.js` (港交所披露易), NOT cninfo. The cninfo downloader (`~/Projects/tinyant/cninfo/index.js`) often returns incomplete HK annual reports (~700KB instead of 7-11MB). Example: `node ~/Projects/tinyant/hkexnews/index.js --codes 02858 --year 2019-2022`. The hkexnews tool downloads complete PDFs directly from the HKEX official disclosure platform.

**Pitfall — "Residual method" for fee decomposition:** When decomposing a bundled fee into components (e.g., SaaS fee = tech fee + channel commission + guarantee fee), the residual (what's left after subtracting known components) is the **least reliable** estimate. Always be explicit: "X% is directly from annual report, Y% is industry benchmark, Z% is residual and therefore uncertain." Never present residual estimates with the same confidence as directly sourced data.

**Pattern — Sub-mode disclosure analysis within business segments**: When a company's annual report discloses that a business segment operates through multiple sub-modes (e.g., Yixin's SaaS business split into "纯技术" mode and "流量+科技" mode), these sub-modes often have dramatically different risk profiles. Key analysis steps:
1. **Extract sub-mode breakdown**: Look for tables or paragraphs that split revenue, transaction volume, or financing amount by sub-mode. Note: companies may only start disclosing sub-modes in certain years (e.g., Yixin started in 2024).
2. **Assess risk-bearing by sub-mode**: Different sub-modes often mean different risk allocation. "Pure tech" mode = partner bears risk; "full-service" mode = company bears risk. The risk profile of the segment depends on the MIX, not the aggregate.
3. **Multi-year sub-mode tracking**: When adding sub-mode analysis to the report, first collect ALL available years of sub-mode data before updating. Check earlier annual reports (even years before the sub-mode was formally named) for retrospective data or descriptions that allow back-calculation.
4. **Risk concentration**: If 90%+ of a segment's volume is in the risk-bearing sub-mode, the segment's risk profile is effectively that of the risk-bearing mode — regardless of how the company labels the overall segment.
5. **Pitfall**: Companies may not disclose sub-mode revenue split (only transaction volume split). In that case, assume revenue is proportional to transaction volume, but note the assumption.

**Pattern — Detecting business reclassification:** When a company's reported metric changes dramatically (e.g., SaaS fee rate jumping from 4.5% to 11.2%), check if the business definition changed. Look for:
1. Historical baselines before the change (e.g., 2023 SaaS was "pure tech" at 4.5% fee rate)
2. Management language about "strategic transformation" or "business model evolution"
3. Whether the metric definition stayed the same or was redefined
If the business was reclassified (e.g., loan facilitation revenue moved into SaaS), the dramatic change reflects accounting, not unit economics. Always establish the "true baseline" before the reclassification.

**Pattern — "Net service rate" analysis for platform businesses:** When analyzing platform/intermediary businesses, look for metrics that separate "gross rate" from "net rate" (after deducting pass-through costs like channel commissions, dealer rebates). Example: Yixin's annual report disclosed "net service rate" (净服务费率) = SaaS revenue minus commissions / transaction volume. This revealed that the 11.2% gross fee was actually 6.1% channel cost + 5.1% net service rate. Without this decomposition, you'd overestimate the company's true value-add.

**Workflow — IMA notes synchronization:** When iterating on a report with the user, update the local .md file first. Only push to IMA notes after the user says "finalize" or explicitly asks to sync. Don't push intermediate versions — it creates version confusion.

**Priority 1 — Company Primary Sources (web search, highest priority when skills are insufficient):**
- Latest annual report / 20-F / 6-K filing
- Latest prospectus (if newly listed)
- Company official website (about page, product/service pages, investor relations)
- **Earnings call transcripts** — critical source for management outlook (Chapter 4)
- **Investor day / capital markets day presentations** — key source for strategic plans (Chapter 4)
- ESG/CSR reports (for risk and governance information)

**Priority 2 — Regulatory & Exchange Sources:**
- Exchange announcements and disclosures
- Regulatory filings (CSRC, SEC, SFC)

**Priority 3 — Third-Party Analysis:**
- Industry research reports (brokerage research, consulting firm reports)
- News articles from reputable financial media
- Company profiles on financial data platforms
- Industry association publications

**Priority 4 — General Context:**
- Market commentary and analyst opinions
- Peer company disclosures for competitive landscape
- Macroeconomic and policy context affecting the industry

- **Data freshness — historical depth**: Free-tier financial data tools (mx-finance-data, etc.) often limit historical lookback to 3 years. For multi-year trend analysis (10+ years), check if the user maintains historical financial databases (e.g., Google Sheets with CIQ/Wind data) that can supplement. Ask the user if longer history is needed and what sources are available before defaulting to the tool's limited window.

**Collection rules:**
- Conduct multiple rounds of targeted searches to cover all aspects
- Extract key facts, figures, and quotes; note the source
- Prioritize primary sources over secondary; prefer recent data over older data
- When sources conflict, present both perspectives with attribution
- **Data freshness — industry data**: Always search for the most recent industry/market data available, even if the company's latest filing is from a prior quarter. Industry data (market size, growth rates, competitive rankings) should be the latest available (within 6 months if possible), sourced from industry reports, research databases, or authoritative market research firms. Do not rely solely on data embedded in older company filings or broker reports.
- **Data freshness — shareholder info**: Shareholder data (前十大股东) MUST come from the LATEST quarterly report (一季报/中报/三季报/年报 whichever is most recent), NOT from the annual report if a more recent quarterly filing exists. Annual report shareholder data is often 3–6 months stale by the time it is published.
- **Data freshness — product revenue breakdowns**: Segment/product-level revenue and margin data MUST come from the LATEST annual report (or latest available brokerage report citing that year's annual report). Do NOT use "the latest available" data from a prior year's filing when a newer annual report has been published. If the annual report does not disclose product-level detail, search for recent brokerage research reports (券商研报) that cite the latest annual report data.
- **Table/list completeness**: When presenting ranked lists, TOP-N tables, or structured rankings, present the complete list from the source. Do NOT selectively show entries (e.g., showing 1-5 and 10-11 but skipping 6-9) — this creates the impression of missing or fabricated data. If the source is incomplete, present what's available and explicitly note the gap.
- **Anti-hallucination (quantitative)**: Never fabricate data or use the model's own knowledge to fill in quantitative claims. If a figure is not found, explicitly note the gap rather than inventing a number. All quantitative data must come from verifiable sources with attribution. Model knowledge may only be used for qualitative context, never as a substitute for sourced data. When complete data cannot be found after searching (e.g., a ranking's 6-20 entries are not publicly available), explicitly state what was searched for, why it's unavailable, and do not list partial entries that create ambiguity.
- **Estimation methodology transparency (剩余法/残差法)**: When decomposing a total figure into components using the residual method (e.g., total fee = component A + component B + remainder), you MUST explicitly label the confidence level of each component:
  - **Directly measured**: Comes from official disclosure with explicit formula (e.g., "净服务费率5.1%" from annual report MD&A with footnote definition)
  - **Estimated via benchmarking**: Derived from industry peer comparison (e.g., "tech fee ~3-4%" based on pure-play peers' take rates)
  - **Residual/remainder**: What's left after subtracting other components (e.g., "guarantee fee ~1-2%" = total 11.2% - net service rate 5.1% - tech fee 3-4%). This is the LEAST reliable component — it may contain measurement error, other unlisted components, or rounding differences.
  
  **Always present in this order**: directly measured → estimated → residual. Never present a residual estimate with the same confidence language as a directly measured figure. Example: "渠道佣金6.1%（年报官方数据，= 综合费率11.2% - 净服务费率5.1%）" vs "担保费约1-2%（剩余法估算，精度有限，可能包含其他服务费用）".
- **Anti-hallucination (qualitative — names, relationships, attributions)**: Never infer specific company names, client names, supplier names, partnership details, or business relationships from industry context or general knowledge. For example, if a company's annual report mentions "ODM/OEM clients" but does not name them, do NOT write "clients include [well-known company in the industry]" based on inference. Only list specific names that appear in sourced disclosures (audit reports, prospectuses, regulatory filings, broker reports citing filings). If names are not publicly disclosed, write "specific client names not disclosed" rather than guessing. This applies equally to: OEM/ODM clients, raw material suppliers, technology partners, distribution partners, and strategic alliances.
- **Annual report financial appendix deep-dive (应收账款/合同资产/减值分析)**: When the user flags a specific balance-sheet risk (e.g., "应收账款太大"), and annual report PDFs are already downloaded, follow this extraction workflow:
  1. **Use `fitz` to find the right pages** — search for keywords ('账龄', '应收账款', '坏账准备', '合同资产', '预期信用损失') to locate the financial notes pages. AR aging tables are typically in 附注五 (合并报表) and 附注十九 (母公司报表). Don't rely on page numbers — they vary by year.
  2. **Extract aging tables** — the aging breakdown (1年以内/1-2年/2-3年/3-4年/4-5年/5年以上) with both 期末 and 期初 values. Compare year-over-year aging migration to detect deterioration (3年以上 segments growing = projects not being collected).
  3. **Extract provision rates by aging bucket** — compare against industry norms. 1年以内 should be 2-5%; if it's 0.3% like FOX Company in the 中材国际 case, flag it as potentially under-provisioned.
  4. **Extract top-5 obligor list** — (前五大欠款方). Note: this combines 应收账款 + 合同资产. Calculate individual client concentration (合计占比) and per-client provision rates. Overseas clients with low provision rates are a red flag.
  5. **Pull multi-year credit impairment loss (信用减值损失)** — from Wind or annual report. A sudden jump (e.g., 中材国际 2025: 信用减值 from 1.23亿→5.76亿, +369%) signals either accelerated provisioning or genuine asset quality deterioration.
  6. **Calculate derived metrics**: (a) "两金" (应收账款+合同资产) as % of revenue and as % of total assets, multi-year trend; (b) aging migration impact — estimate next-year additional provisions from natural bucket migration (e.g., 3-4年→4-5年 increases provision rate from ~20% to ~38%); (c) provision adequacy stress test — what happens if 1年以内 provision rate is raised from X% to Y%?
  7. **Always compare against same-sector peers** — an EPC company with 应收占营收 48% is normal for the industry class; a manufacturing company at the same ratio would be alarming. Use Wind batch query to pull peer AR data for context.
  See `references/annual-report-financial-deep-dive.md` for a worked example (中材国际 2025 AR analysis).

- **Business model authenticity assessment (收入质量诊断)**: When a company claims a strategic transformation (e.g., "from lending to SaaS", "from manufacturing to platform", "from product to service"), do NOT take the narrative at face value. Apply three diagnostic tests before accepting the transformation story. See `references/revenue-quality-diagnostics.md` for the full framework and industry benchmarking data:
  1. **Fee rate / take rate benchmarking**: Compare the company's effective service fee rate against pure-play peers in the claimed business model. If the company claims "SaaS/tech platform" but its take rate is 2-3x the industry norm for pure tech services, the label is likely repackaging. Example: 易鑫 claims SaaS transformation at 11.2% take rate, while pure auto-lending platforms (灿谷 3.9-4.2%, 信也 3.3-3.4%) operate at 3-4% — the gap reveals channel commissions + risk premium embedded in the "SaaS" fee.
  2. **Cost structure consistency**: If a business is truly "asset-light tech/SaaS", its cost-of-revenue ratio should be low (20-40%). If the cost ratio is 70-80%+ despite the "tech" label, the business still carries heavy operational/channel costs inconsistent with the narrative.
  3. **Revenue migration pattern**: When one revenue line declines sharply while a new line grows at a similar absolute amount (e.g., 贷款促成 -41% while SaaS +150% in absolute terms), suspect business reclassification rather than genuine new business creation. Ask: are the same customers, channels, and funders involved?
  **When to apply**: Any time a company's report uses buzzwords like "SaaS转型", "轻资产转型", "科技赋能", "平台化", "数字化服务" to describe a shift in revenue composition. The user is likely to ask probing questions about whether the transformation is real — have the analysis ready in the report rather than discovering gaps later.
- **A+H dual-listed companies**: When the target is listed on both A-share and HKEX (e.g., 605499.SH + 09980.HK), be aware of the following:
  - **Accounting standard differences**: A-share uses CAS (Chinese Accounting Standards), H-share uses IFRS/HKFRS. Financial figures (net profit, equity) may differ slightly between the two. **Always prefer A-share data as the primary source** for Chinese-domiciled companies — it is more granular and the basis for domestic regulatory filings.
  - **Shareholder data**: A-share 前十大股东 and H-share substantial shareholders reflect different investor bases. Present the A-share shareholder table for the main ownership structure, and note H-share holdings (e.g., HKSCC Nominees) as a separate item.
  - **Stock price/market cap**: Report both A-share (CNY) and H-share (HKD) valuations separately. H-share often trades at a discount (AH溢价). Note the listing dates and IPO prices if relevant (e.g., H股破发情况).
  - **Cash flow and balance sheet**: The H-share cash flow statement may include items like foreign exchange impact (汇率影响) that do not appear in A-share reports. Reconcile if presenting cross-market analysis.

- **Pure HK-listed Chinese companies (非A+H)**: For companies listed only in HK but operating entirely in mainland China (e.g., incorporated in Cayman, operations in PRC, reporting in RMB):
  - **Currency pitfall in mx-finance-data**: Summary metrics (营收/净利润/总资产/毛利率) are returned in HKD, but detailed financial statements (cash flow, balance sheet items) are returned in RMB. Always check the "原始币种" row in detailed tables to confirm currency before mixing data sources. When writing the report, standardize on RMB for operating metrics and note HKD equivalents only for market-level data (stock price, market cap).
  - **Shareholder disclosure**: Unlike A-share companies, there is no "前十大股东" format. Use the HKEX "Substantial Shareholders" disclosure (5%+ threshold) from the annual report or HKEX权益披露公告. Many such companies have highly concentrated ownership through offshore holding structures (e.g., Cayman/BVI entities controlled by founders).
  - **Annual report access**: HKEX annual reports follow IFRS/HKFRS format. The MD&A section is labeled "Management Discussion and Analysis" and is typically less detailed than A-share equivalents. Supplement with brokerage research reports (via mx-finance-search) for granular business segment data.
- **Recent M&A handling**: If the target company has acquired or merged with other listed companies within the last 1–2 years (e.g., 华润三九 acquiring 天士力 and 昆药集团), you MUST query each entity's financial data separately (target + each subsidiary). Consolidated reports may blend pre- and post-acquisition periods, making YoY comparisons misleading. When writing the Business Deep Dive (Section 2), create separate subsections for the parent and each major acquired entity, with standalone financials, strategic roles, and integration status. Note which data points are consolidated vs. pro-forma vs. standalone. In the risk section, flag goodwill impairment risk and integration execution risk.
- **Terminated/failed M&A handling**: Also track M&A deals that were announced but later terminated (e.g., 涪陵榨菜 announced 味滋美 51% acquisition in April 2025, terminated October 2025 due to "核心商业条款未达成一致"; the target was then acquired by 中炬高新). Terminated deals reveal management's strategic intent and competitive landscape dynamics — include them in Section 2 (business strategy) or Section 5 (M&A execution risk). Note: the terminated deal's target may be acquired by a competitor, changing the competitive landscape.
- **DPS adjustment through stock splits (转增)**: When the company has had stock splits/bonus shares (e.g., 10转6, 10转5, 10转3), per-share metrics (EPS, DPS, 每股净资产) from before and after the split are NOT directly comparable. When presenting multi-year per-share data, either adjust historical figures retroactively (multiply pre-split figures by the split factor) or clearly annotate which year the split occurred and warn readers. Dividend payout ratios (% of net profit) are unaffected and safe to compare directly. For DPS specifically, retroactively adjust to maintain consistency with EPS and calculate accurate payout ratios — do NOT use raw nominal DPS if splits occurred.

- **Pattern — Cross-company comparison framework**: When the user has already done a deep-dive on Company A (e.g., a successful "second curve" case like 宇通客车's overseas expansion) and asks to evaluate Company B using A as a benchmark ("我想顺着XX的成功经验，看看YY"), follow this approach:
  1. **Don't start from scratch** — use the analytical lens from Company A to structure the comparison. Key dimensions to compare: business model (product vs project), overseas margin premium (or lack thereof), ROE gap, moat type (manufacturing brand premium vs engineering cost advantage), and growth trajectory shape (true second curve vs U-shaped reversion).
  2. **Lead with the conclusion** — the user is experienced and wants to know quickly whether Company B is "the next A" or a fundamentally different animal. Start with a "核心结论（先行）" section that explicitly answers: is the comparison valid? Where does the analogy hold and where does it break?
  3. **Quantify every dimension** — side-by-side tables comparing key metrics (海外占比 trajectory, 毛利率 differential, ROE, PE/PB). The data differences tell the story better than prose.
  4. **Differentiate "surface similarity" from "structural similarity"** — both companies may have "海外占比超过50%" but the *reason* matters enormously. A company whose overseas share rose organically from 13%→51% (true second curve, like 宇通) is fundamentally different from one whose share reverted from 82%→37%→55% (passive dilution + recovery, like 中材国际). Always chart the multi-year trajectory, not just the endpoint.
  5. **Tailor the moat assessment to the business model class** — manufacturing companies (product-based, brand premium possible, recurring service revenue) have structurally different moat profiles from engineering/EPC companies (project-based, competitive bidding each time, no brand premium in招投标). Do not apply the same moat template mechanically.

- **Equipment company leading indicators (合同负债 + 产销量)**: For hardware/equipment companies (医疗器械、工业设备、消费电子等), two metrics from the annual report are powerful leading indicators:
  1. **合同负债（预收账款）**: Represents advance payments from customers before delivery. A growing contract liability signals strong order backlog and future revenue visibility. 联影医疗案例: 合同负债29.75亿元（+39.1%YoY）验证了在手订单充裕。
  2. **产销量库存表**: Annual reports include a production/sales/inventory table (生产量/销售量/库存量). Inventory build-up in a specific product line (e.g., MR库存+63.7%) can signal either ramping for strong demand OR potential overstocking — requires cross-checking with subsequent quarters. Query via wind-mcp: `688271.SH2025年年报生产量销售量库存量` or extract from the annual report announcement.

- **Financial/fin-tech company asset quality analysis**: When the target company has lending, financing, or credit guarantee operations (融资租赁, 助贷平台, 消费金融, 小贷, etc.), asset quality analysis is as important as revenue/profit analysis. Key areas to investigate:
  1. **NPL/逾期率 trends**: Multi-year trajectory of non-performing loan ratios. Compare against industry benchmarks (银行auto loans ~0.5-1%, 汽车金融公司 ~0.25%, 融资租赁 ~1.5-3%).
  2. **Provision coverage adequacy**: 拨备覆盖率 should be >200% for safety. Calculate both on-balance-sheet and total (含表外担保) coverage.
  3. **Off-balance-sheet guarantees (表外担保)**: Many fin-tech platforms provide implicit credit guarantees to partner banks. Check annual report notes for "风险保证负债", "融资担保", "信用增级". Compare guarantee balance against on-balance-sheet loans — a ratio >2x is a red flag.
  4. **ECL three-stage migration**: IFRS 9/CAS 22 staging data from annual report appendices reveals portfolio health. Stage 2 proportion <1% may indicate insufficient early warning; Stage 3 loss rate reveals collateral recovery quality.
  5. **CIQ "Industry Specific" sheet**: For financial companies, this CIQ sheet contains NPL, allowances, charge-offs, and loan composition data NOT in standard IS/BS/CF sheets. Always check it.
  6. **Credit rating reports (联合资信/中诚信)**: For companies with onshore operating subsidiaries, rating agency reports provide 3-5 year histories of 不良率, 拨备覆盖率, and leverage that are unavailable elsewhere. Search `web_search("联合资信 <子公司名称> 主体评级报告")`.
  
  See `references/financial-company-asset-quality.md` for the full methodology, scorecard, industry benchmarks, and a case study (易鑫集团).

- **wind-mcp global_stock_data reliability**: The `global_stock_data` server for HK/US stocks is less reliable than `stock_data` for A-shares. Common failure modes: (1) Returns QUERY_FAILED for valid queries — try dropping leading zeros from stock codes (e.g., `02858.HK` → `2858.HK`). (2) Historical multi-year queries may return null for some metrics — fall back to CIQ Financials + web search for asset quality data. (3) Some metrics (拨备覆盖率, 逾期率, 不良贷款率) are NOT available through wind-mcp for HK stocks — use credit rating reports and annual report appendices instead. Always have a fallback plan when researching HK-listed financial companies.

- **Fintech/platform company funding source analysis**: When analyzing companies that operate as fintech platforms or loan facilitation platforms (e.g., Yixin Group, Lufax, etc.), be aware that annual reports often disclose the NUMBER of cooperation partners (e.g., "近75家各类银行、金融租赁公司及主机厂") but NOT the specific proportion breakdown by institution type (banks vs. financial leasing companies vs. consumer finance companies). This is a common data gap. To work around:
  1. Check if the report mentions "资本提供者日益多元化" or similar language — this often signals a shift from banks (lower funding cost) to leasing companies (higher funding cost), which can be inferred from fee rate changes
  2. Look for "核心客户" (core customer) data — the number of core customers and average revenue per core customer can indicate concentration
  3. Check investor presentations or earnings call transcripts for more detailed breakdowns
  4. If the proportion is critical to the analysis, note it as a data gap and recommend contacting IR
  For auto finance platforms specifically, funding cost differentials matter: banks (2.0-2.5%), AAA-rated leasing companies (2.3-2.5%), AA+ rated (2.8-3.5%), small/medium leasing (4-6%+). A shift toward leasing companies as funding sources will push up the platform's综合费率 (comprehensive fee rate).
- **wind-mcp announcement content extraction**: `get_company_announcements` returns the announcement **body text** (not just metadata), making it a powerful primary source. For annual reports, use targeted keywords to extract specific chapters:
  - `"688271.SH 2025年年报 经营情况讨论与分析"` → MD&A chapter with management's performance attribution, expense analysis, strategic outlook
  - `"688271.SH 风险因素 关税 海外 贸易限制 2025年年报"` → Risk factors chapter with company's own risk assessment
  - `"688271.SH 2025年年报 产品 销售"` → Product/sales breakdown tables
  Set `top_k=3~5` for multi-chapter extraction. This is more reliable than web_search for getting exact financial table data from filings.

### Step 2.5: Identify Financial Business Lines (When Applicable)

**For companies with significant financial/credit business** (auto finance, consumer lending, leasing, factoring, etc.), conduct a parallel "financial institution analysis" using banking-style metrics. Many "tech platform" companies have substantial financial business lines that should be analyzed separately.

**Key financial metrics to extract:**

1. **Net Interest Margin (NIM/净息差)**:
   - Formula: (Interest Income - Cost of Funds) / Average Interest-Earning Assets
   - For auto finance: (融资租赁收入 - 资金成本) / 季度平均应收融资租赁款
   - Compare against: banks (2-2.5%), financial leasing companies (2.5-3.5%)

2. **Asset Quality Metrics**:
   - **NPL Ratio (不良率)**: Non-Performing Loans / Total Loans
   - **Provision Coverage (拨备覆盖率)**: Total Allowance / NPL (target >150%, ideally >200%)
   - **Provision Ratio (拨备/总贷款)**: Total Allowance / Total Loans
   - **Net Charge-Off Rate (净核销率)**: Net Charge-Offs / Total Loans
   - **ECL Three-Stage Data**: Stage 1 (正常), Stage 2 (关注), Stage 3 (不良) with loss rates

3. **Leverage & Capital**:
   - **Leverage Ratio (杠杆倍数)**: Total Assets / Equity (banks: 10-15x, leasing: 6-10x)
   - **Debt-to-Equity**: Total Debt / Equity
   - **Asset-Liability Ratio (资产负债率)**: Total Liabilities / Total Assets

4. **Profitability**:
   - **ROA (总资产收益率)**: Net Income / Average Total Assets
   - **ROE (净资产收益率)**: Net Income / Average Equity
   - Compare against banking sector benchmarks

**Data extraction patterns:**
- **ECL three-stage data**: Found in annual report notes (附注), typically under "预期信用损失" or "金融风险管理" sections
- **Interest income/cost breakdown**: In MD&A section, look for "利息收入", "资金成本", "净息差" tables
- **NPL/provision data**: CIQ "Industry Specific" sheet has Non-Accrual Loans, Allowances, Charge-Offs
- **Historical trend**: Extract 3-5 years to show trajectory

**Analysis framework:**
- Compare the company's financial metrics against actual banks and financial leasing companies
- Identify advantages (e.g., higher NIM from specialized lending) and risks (e.g., lower leverage = underutilized capital)
- Flag if provision coverage is below regulatory thresholds (<150%) or safety thresholds (<200%)
- Note if Stage 3 loss rates are rising (indicates deteriorating recovery rates)

**Pitfall — "Tech platform" companies with financial business**: Companies like Yixin (auto finance platform) may present themselves as "tech companies" but have substantial credit risk exposure through guarantees, SaaS services with implicit guarantees, or direct lending. Analyze BOTH the tech platform metrics (GMV, take rate, merchant count) AND the financial institution metrics (NIM, NPL, provision coverage). The two analyses may reveal different risk profiles.

See `references/financial-institution-metrics.md` for detailed extraction patterns and benchmark comparisons.

- **Overseas expansion claims — verify regulatory status**: When a company claims to have "entered" or "become top N in" an overseas market (e.g., "跻身新加坡前三"), do NOT treat this as evidence of regulatory compliance strength. Key verification steps:
  1. **Check licensing**: Does the company hold the relevant financial/business license in that jurisdiction? (e.g., MAS license for Singapore financial services)
  2. **Understand the regulatory gap**: Many jurisdictions have lighter regulation for "tech platforms" or "matchmaking services" vs direct lending. A company may operate in a regulatory gap rather than passing strict compliance.
  3. **Market size context**: "Top 3 in Singapore" may mean very small absolute scale — Singapore's auto finance market is tiny due to COE quota system.
  4. **Business model difference**: Overseas operations may use a different model (pure SaaS/tech vs risk-bearing) that has lower compliance requirements.
  5. **No enforcement action ≠ compliance excellence**: Absence of regulatory penalties only shows no obvious violations, not a robust compliance framework.
  - **Example**: Yixin claimed "top 3 non-bank auto finance provider in Singapore" — but Singapore's MAS does not directly regulate car dealers or pure tech platforms. Yixin's XPort system likely operates as a matchmaking platform (not a lender), so MAS licensing may not be required. The claim reflects market share, not regulatory endorsement.

### Step 3: Write the Research Report

Structure the report following the template in `references/report-template.md`. Load this file for the full template with detailed section-by-section guidance.

**Cross-market comparison (recommended when applicable):**
When the target company faces structural headwinds (e.g., population decline, digital disruption, industry maturity) that have already played out in analogous markets, proactively research and include a cross-market comparison as an appendix. For example:
- Chinese consumer/education companies → compare against Japanese peers (Japan faced similar trends 20-30 years earlier)
- Emerging market fintech → compare against US/UK precedents
- Companies facing regulatory tightening → compare against markets that went through similar cycles
This analysis transforms abstract risk discussion into concrete historical evidence. Search for how analogous companies adapted (overseas expansion, premiumization, consolidation, diversification) and what the outcomes were. Present the comparison in an appendix with explicit mapping to the target company's current situation.

**Key writing principles:**
- Use simple, plain language to explain business models — imagine explaining to an intelligent non-specialist
- For each business segment, clearly explain: what it is, who the customers are, what inputs/resources are needed, how revenue is generated, and the business flow/process
- Support all factual claims with specific data points and cite sources
- Maintain a neutral, objective tone throughout — present facts and analysis, not opinions or recommendations
- Highlight information gaps transparently
- Use Chinese (中文) as the default output language unless the user requests otherwise
- **Writing format — narrative over bullet (critical user preference):** The user explicitly corrected that bullet points are too condensed — they assume prior knowledge and compress away the reasoning. When explaining strategic concepts, business model details, or analytical conclusions, use **narrative paragraphs**, not bullet lists. The pattern is: **conclusion-first** (state the key finding in one sentence), then **narrative expansion** (2-4 paragraphs explaining what it means, why it matters, and how it works in plain language). For example, instead of listing "宇通模式" as "- 卖产品→技术输出→KD组装", write 3 paragraphs explaining what KD assembly is, how it changes the business model from one-time sales to ongoing technology licensing, and why it creates a competitive moat. Bullet lists are acceptable for raw data tables, financial metrics, and short factual enumerations — NOT for explaining concepts, strategies, or analytical reasoning.
- **Attribution discipline (critical):**
  - **Do not invent packaging phrases** that sound like research report quotes (e.g., "销售真空期", "阵痛期深水区"). Use plain descriptive language or quote the source directly with attribution.
  - **Do not reduce multi-factor causality to a single cause.** If a metric change (e.g., revenue drop) has multiple drivers (policy + channel reform + industry cycle), list all factors. Never say "X导致Y" when the reality is "X、Y、Z多重因素共振".
  - **When using company/management language, mark it as such.** Distinguish between: company原话 ("短期会有阵痛"), 媒体报道 ("终端覆盖出现暂时性断层"), and analyst包装 ("销售真空期"). Do not mix these without attribution.
  - **Data points require source tags.** Every quantitative claim (revenue, %, ranking) must have an inline source note on first mention.

### Step 4: Review and Deliver

**Output**: `./[stock code/ticker]_[company name]_深度研究报告_YYYYMMDD_HHmm.md`

**MANDATORY — Appendix-to-Body Consistency Review:**
When the report includes deep-dive appendices (e.g., asset quality stress tests, revenue quality diagnostics, take rate decomposition) that reveal findings contradicting the main body's initial characterizations, you MUST systematically review and correct the main body BEFORE delivery. This is the #1 quality failure mode in iterative research: appendices contain the truth, but the body still repeats the initial (wrong) narrative.

**Consistency check procedure:**
1. List every major characterization in the body (e.g., "轻资产转型", "SaaS纯科技平台", "技术驱动护城河")
2. For each, check whether any appendix finding contradicts it
3. If contradiction exists, update the body with corrections AND add inline cross-references to the appendix (e.g., "详见附录D/E/F")
4. Pay special attention to: Section 2 (business descriptions), Section 3.4 (moat assessment), Section 4.1-4.2 (management narrative acceptance), Section 5 (risk factors)

**Example from 易鑫集团 research:** Appendices D/E/F revealed that "SaaS" was actually "guaranteed loan facilitation" with 864B off-balance-sheet obligations. But the body still described it as "轻资产纯科技平台" with "技术驱动护城河" — requiring 11 corrections across 6 sections. This review step would have caught all of them in one pass.

**MANDATORY — Data Freshness Audit before delivery:**
After writing the initial draft, run through this checklist. Every data point must be verified against the most recent available source:

1. **Financial metrics (营收/净利润/毛利率/ROE):** Are you citing the latest annual report? If a newer quarterly report exists, have you added Q1/H1/Q3 updates inline? Never cite a prior year's annual report or a brokerage report quoting old data when the latest annual report has been published.
2. **前十大股东 (Top 10 shareholders):** MUST use the latest quarterly report data (一季报/中报/三季报 whichever is most recent). Annual report shareholder data is stale by 3-6 months at publication time. Use wind-mcp-skill `get_stock_equity_holders` with the current quarter (e.g., "2026年一季报前十大股东").
3. **子公司财务数据 (Subsidiary financials):** Use the latest annual report's "主要控股参股公司分析" section, not prior year reports or brokerage estimates.
4. **应收账款/商誉/借款 (Balance sheet items):** Pull from the latest annual report or quarterly report via wind-mcp-skill, not from older filings.
5. **Market data (股价/市值/PE/PB):** Always use real-time/current-day data, never embed stale numbers from a filing.
6. **Citation audit:** Scan every `[Source: ...]` tag in the draft. Any citation referencing a prior-year annual report (e.g., "Source: 2024年年报") when a newer annual report is available must be replaced with the current year's data or explicitly marked as "latest available."
7. **User complaint pattern:** If you find yourself citing "2024年报" or "2025年一季报点评" for any metric when 2025年报 or 2026年一季报 data exists, that's a failure — go back and update.

**MANDATORY — Business Model Consistency Audit (appendix-driven corrections):**
After writing appendices or deep-dive analyses (e.g., regulatory impact, asset quality stress tests, business model decomposition), **go back and re-read every main body section** (especially §1.3 Company Profile, §2 Business Deep Dive, §3.4 Moat, §4 Management Outlook, §5 Risk Factors) to check whether appendix findings contradict earlier characterizations. Common patterns that require correction:
- Appendix reveals a "light-asset" business is actually heavy-risk (e.g., SaaS with hidden guarantee obligations) → correct §2 segment descriptions, §4 "asset-light transformation" narrative, §5 risk tables
- Appendix reveals a "tech platform" is actually a channel/guarantee business → correct §3.4 moat assessment (downgrade tech moat, upgrade channel moat)
- Appendix reveals regulatory threat not mentioned in main body → add to §5 risk factors as top-priority risk
- Appendix reveals management narrative is misleading → add critical cross-check in §4.1

This audit is critical because the main body is written first with incomplete understanding, and appendices often uncover the real business model. Without this step, the report contains internal contradictions that undermine credibility.

Before delivering the report:
- Verify all stock codes and company names are accurate
- Check that each major section has substantive content (not just placeholder text)
- Ensure risk factors section is thorough and well-organized
- Confirm the report is self-contained and readable without external references
- Save the Markdown file to the current workspace root directory and present it to the user

### Step 5: Multi-Company Parallel Research (When Applicable)

When the user asks to research multiple companies in the same industry simultaneously (e.g., "研究威高血净、健帆生物、山外山、宝莱特"), use the parallel subagent workflow described in `references/multi-company-research-pattern.md`.

**Key pattern:**
1. **Phase 1**: Delegate individual company reports to subagents in parallel (up to 3 concurrent)
2. **Phase 2**: Cross-reference competitor comparison tables across all reports (MANDATORY — each subagent's competitor data for other companies will be stale/estimated)
3. **Phase 3**: Main agent produces industry-level report using company reports as data sources

**Critical**: After Phase 1, always do Phase 2 before Phase 3. Without the cross-reference pass, the industry report will inherit inconsistent market cap, revenue, and market share data from company reports.

### Step 6: Optional — Product Image Enhancement

For companies with physical products, you may optionally enhance Section 2 (Business Deep Dive) with product images **after** the main report is complete. This is cosmetic only — it adds illustrative images without altering any analysis.

Load `references/product-images-enhancement.md` for the full workflow. Skip this step unless the user requests it, or the company's products are highly visual and the website has accessible product pages.

## Research Framework

The report covers five core pillars (in order):

1. **Company Overview** — Basic profile, listing info, market position summary
2. **Business Deep Dive** — Detailed breakdown of each business segment
3. **Industry & Competitive Landscape** — Industry overview, value chain, competitive dynamics
4. **Management Outlook & Strategy** — Management's view, strategic plans, track record credibility
5. **Risk Factors** — Business, financial, industry, regulatory, and other risks

**Explicitly excluded:** Financial statement modeling, valuation, stock ratings, target prices, investment recommendations.

## Resources

### references/
- `report-template.md` — Full report template with detailed section-by-section writing guidance and formatting instructions. Load this file when writing the report.
- `product-images-enhancement.md` — Workflow for enhancing Section 2 with product images.
- `ma-data-collection.md` — Pattern for collecting and attributing data when the target company has recent M&A activity (acquired subsidiaries, mergers). Query each entity separately; map acquisition timeline; flag goodwill/integration risks.
- `cross-market-comparison-framework.md` — Structured framework for cross-market comparison in two modes: (Type 1) consumer goods companies facing demographic headwinds — covers inflection point identification, post-inflection profit tracking methodology (use operating profit, not net profit), strategy archetypes (functionalization, globalization, B2B pivot, pharma crossover), compiled findings from Japan/Korea/Europe dairy companies; (Type 2) financial services/fintech companies — covers market structure comparison (penetration rates, interest rates, business models), regulatory environment benchmarking, and strategic positioning vs international peers. Load this when writing Appendix cross-market comparisons.
- `cross-company-benchmark-comparison.md` — Framework for Type 3 comparison: using a previously-analyzed company as a yardstick to evaluate a new target at a superficially similar inflection point (e.g., "Company B also crossed overseas > domestic, is it the next Company A?"). Covers: trajectory verification (organic second-curve vs U-shape reversion), profitability decomposition (margin spread test), business model compatibility check. Includes the 宇通 vs 中材国际 case study. Load when the user frames a new research target in reference to a previously-analyzed company's success pattern.
- `niusan-background-investigation.md` — Reference guide for researching backgrounds of natural person shareholders (牛散) in A-share companies. Includes search patterns, known cases (张弦、沈奇迪、葛卫东等), and pitfalls (同名异人、代持、滞后性). Load when user asks about shareholder backgrounds.
- `wind-mcp-query-patterns.md` — Proven Wind MCP query patterns for A-share/HK/US listed company research: 前十大股东, 行情快照, 财务数据, 公司公告, 股本结构. Includes shell quoting rules, NL question format constraints, and segment revenue null-padding pitfall.
- `multi-company-research-pattern.md` — Parallel subagent workflow for researching 3+ companies in one industry simultaneously, then cross-referencing competitive data, then synthesizing an industry report. See Step 5 for usage.
- `new-controlling-shareholder-governance.md` — Framework for analyzing a new controlling shareholder's governance track record when ownership changes (state-owned acquirer, restructuring, etc.). Covers governance style, past integration cases, initial performance, and pros/cons assessment. Load when user asks about new acquirer's management history.
- `profit-decomposition-framework.md` — Analytical framework for "增收不增利" situations: decompose gross margin changes (cost vs revenue growth divergence), identify structural drivers (医保降价, product mix shift, capacity ramp-up), and cross-validate management's expense-based narratives. Load when user asks about profit decline reasons or "增收不增利".
- `chemical-industry-cycle-analysis.md` — Chemical industry cycle position framework: PPI/CIP/capex bottom signals, MDI 25-year cycle pattern, domestic+overseas supply-side changes, demand transition, oil price impact matrix. Load when user asks for cycle analysis of a chemical/petrochemical company.
- `cninfo-annual-report-extraction.md` — Workflow for downloading A-share annual reports via cninfo (--annual flag required) and extracting MD&A sections from PDFs. Covers: TOC-vs-content pitfall, section number variation (第三节 vs 第四节), end-marker detection, keyword filtering for thematic analysis. Load when extracting multi-year MD&A content from annual report PDFs.
- `global-auto-finance-market-comparison.md` — Cross-market reference data for auto finance company research: market size, penetration rates, interest rates, key players, regulatory environments, and business models across USA/Europe/UK/Germany/Japan/Australia/China. Includes funding cost differentials (banks vs. leasing companies) and strategic implications for Chinese fintech platforms. Load when researching auto finance or loan facilitation platform companies.
- `pharma-annual-report-product-breakdown.md` — Pharma annual report data hierarchy for product-line revenue: 主营构成 (too coarse) → 核心产品毛利率表 (detailed by 治疗领域, includes peer comparison) → 子公司经营数据 (revenue + net profit by subsidiary). Two extraction methods: (1) cninfo PDF + pymupdf find_tables() for complete structured tables (preferred), (2) ifind-repilot RAG for quick lookups. Load when analyzing pharma company product-level revenue/margins.
- `derived-metrics-extraction.md` — How to find and extract management-defined derived metrics (净服务费率, 净息差, take rate, ARPU, etc.) from annual report MD&A sections. Covers: search patterns, footnote formula extraction, multi-year trend building, fee decomposition via residual method, and benchmarking against pure-play peers. Load when analyzing platform/financial companies with complex fee structures.
- `reverse-engineering-ratio-formulas.md` — Technique for reverse-engineering hidden denominators from disclosed ratio formulas. When annual reports disclose a ratio formula (e.g., net service fee rate = (revenue - commission) / financing volume) but not the denominator, you can back-calculate it if you know all other components. Includes worked example (Yixin platform financing volume) and pitfalls (rounding, segment reclassification). Load when you need a metric that isn't directly disclosed but can be derived from disclosed components.
- `revenue-quality-diagnostics.md` — Framework for testing whether a company's claimed business model transformation (e.g., "from lending to SaaS", "from product to platform") is genuine or repackaging. Three diagnostic tests: fee rate benchmarking against pure-play peers, cost structure consistency check, revenue migration pattern analysis. Includes industry take rate benchmarks for Chinese fintech/lending platforms (灿谷, 信也, 奇富, 乐信). Load when user questions whether a "transformation" narrative matches economic reality.
- `financial-company-asset-quality.md` — Methodology for analyzing asset quality of financial/fin-tech companies (融资租赁, 助贷平台, 消费金融, etc.). Covers: CIQ "Industry Specific" sheet data extraction, credit rating reports as data source (联合资信/中诚信), IFRS 9 ECL three-stage migration analysis, off-balance-sheet guarantee assessment, provision coverage adequacy scorecard, industry benchmarks. Includes case study (易鑫集团). Load when target has lending/financing/guarantee operations.
- `references/financial-company-ecl-analysis.md` — Credit impairment loss (信用减值亏损) extraction patterns for financial companies: annual report MD&A provision breakdown table (应收融资拨备/风险保证负债拨备/其他应收款项拨备), capital provider structure analysis (bank vs 融资租赁 funding cost differentials), ECL three-stage model interpretation, key ratios (表外/自营比, 全口径拨备覆盖率). Includes common data gaps (capital provider proportion rarely disclosed) and auto finance platform-specific metrics. Load when researching financial platform companies with credit/guarantee exposure.
- `references/annual-report-financial-deep-dive.md` — Workflow for deep-diving balance-sheet risks (AR aging, provision adequacy, contract assets, top obligors) from annual report PDF appendices using PyMuPDF. Includes derived metric calculations (aging migration pressure, "两金" ratio), worked example (中材国际 2025), and pitfalls. Load when user flags specific balance-sheet risk concerns and annual report PDFs are available.
- `accounts-receivable-risk-analysis.md` — Methodology for deep-diving AR risk in non-financial companies (EPC, construction, equipment). Covers: multi-year "两金" (AR + contract assets) trend, aging table extraction from PDF notes, aging migration provision impact, top-5 counterparty analysis, and the "slow bleed" assessment pattern. Load when AR > 30% of revenue or user mentions AR concerns.
- `cross-company-benchmark-comparison.md` — Framework for comparing a new research target against a previously-analyzed company's success pattern. Three tests: trajectory verification (organic second-curve vs U-shape reversion), margin spread (does overseas earn a premium?), and business model compatibility. Includes 宇通 vs 中材国际 case study. Load when user says "顺着X公司的经验看看Y公司".

## Living Document Workflow

After the initial report is delivered, the user frequently asks follow-up questions (e.g., "2018年亏损原因？", "2025年收入下降原因？", "新股东治理记录如何？"). The workflow is:

1. **Answer the question** with sourced research.
2. **Append as Q&A** to the report's appendix section (typically `附录E` or create one if absent). Use `### E1.`, `### E2.`, etc. numbering.
3. **Re-save the report** to the same file path.
4. **Re-upload to IMA notes** if the user requests (the user maintains a "投研笔记" folder in IMA). See `ima-skill` for the upload workflow.
5. **Commit to the skills repo** if any reference files or the skill itself was modified.

Each Q&A entry should be self-contained and cite sources inline. The report is a living document that evolves across sessions.

**Workflow preference — batch data collection before report updates**: When the user asks to add new data to the report (e.g., "add ECL breakdown", "add sub-mode analysis"), first collect ALL available historical data across multiple years/annual reports BEFORE making any patches to the report. Do not update the report incrementally as each year's data is found. The user prefers to see the complete multi-year picture before deciding what goes into the report. This is especially important for:
- Credit impairment / ECL breakdown tables (need 3-5 years for trend analysis)
- Business sub-mode splits (may only be disclosed in recent years, need to check earlier reports)
- Financial metrics (NIM, leverage, ROA/ROE trends)
- Any data the user wants to compare across years
