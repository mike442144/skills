---
name: listed-company-research
description: "This skill should be used when the user wants to conduct in-depth fundamental research on any listed company, including A-share (China), Hong Kong-listed (HKEX), or US-listed (NYSE/NASDAQ) stocks. Triggers include phrases like '研究XX公司', '分析XX', 'XX公司基本面', '帮我看看XX', 'deep dive XX', 'research XX company', '管理层展望', '战略规划', 'management strategy', 'outlook', or when the user provides a stock code/ticker and asks for analysis. The skill produces a comprehensive, neutral-analysis Markdown report covering company overview, detailed business decomposition, industry competitive landscape, management outlook & strategy, and risk factors. It does NOT include financial statement modeling, valuation, stock ratings, or investment recommendations."
metadata:
  author: Mike Chen
  version: '1.3'
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

### Step 2: Collect Information

Gather as much publicly available information as possible. **Always follow this priority order:**

**Priority 0 — Installed Skills (check first before anything else):**
Before initiating any web search, scan all currently installed skills for any relevant to this research task (financial databases, document parsers, browser automation, etc.). Use installed skills as the primary data source; only fall back to web search when they cannot provide sufficient data. Track every skill invoked (name, data provided, report section) for the "Sources & Limitations" section.

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

**Collection rules:**
- Conduct multiple rounds of targeted searches to cover all aspects
- Extract key facts, figures, and quotes; note the source
- Prioritize primary sources over secondary; prefer recent data over older data
- When sources conflict, present both perspectives with attribution
- **Data freshness — industry data**: Always search for the most recent industry/market data available, even if the company's latest filing is from a prior quarter. Industry data (market size, growth rates, competitive rankings) should be the latest available (within 6 months if possible), sourced from industry reports, research databases, or authoritative market research firms. Do not rely solely on data embedded in older company filings or broker reports.
- **Data freshness — shareholder info**: Shareholder data (前十大股东) MUST come from the LATEST quarterly report (一季报/中报/三季报/年报 whichever is most recent), NOT from the annual report if a more recent quarterly filing exists. Annual report shareholder data is often 3–6 months stale by the time it is published.
- **Data freshness — product revenue breakdowns**: Segment/product-level revenue and margin data MUST come from the LATEST annual report (or latest available brokerage report citing that year's annual report). Do NOT use "the latest available" data from a prior year's filing when a newer annual report has been published. If the annual report does not disclose product-level detail, search for recent brokerage research reports (券商研报) that cite the latest annual report data.
- **Table/list completeness**: When presenting ranked lists, TOP-N tables, or structured rankings, present the complete list from the source. Do NOT selectively show entries (e.g., showing 1-5 and 10-11 but skipping 6-9) — this creates the impression of missing or fabricated data. If the source is incomplete, present what's available and explicitly note the gap.
- **Anti-hallucination**: Never fabricate data or use the model's own knowledge to fill in quantitative claims. If a figure is not found, explicitly note the gap rather than inventing a number. All quantitative data must come from verifiable sources with attribution. Model knowledge may only be used for qualitative context, never as a substitute for sourced data. When complete data cannot be found after searching (e.g., a ranking's 6-20 entries are not publicly available), explicitly state what was searched for, why it's unavailable, and do not list partial entries that create ambiguity.
- **A+H dual-listed companies**: When the target is listed on both A-share and HKEX (e.g., 605499.SH + 09980.HK), be aware of the following:
  - **Accounting standard differences**: A-share uses CAS (Chinese Accounting Standards), H-share uses IFRS/HKFRS. Financial figures (net profit, equity) may differ slightly between the two. **Always prefer A-share data as the primary source** for Chinese-domiciled companies — it is more granular and the basis for domestic regulatory filings.
  - **Shareholder data**: A-share 前十大股东 and H-share substantial shareholders reflect different investor bases. Present the A-share shareholder table for the main ownership structure, and note H-share holdings (e.g., HKSCC Nominees) as a separate item.
  - **Stock price/market cap**: Report both A-share (CNY) and H-share (HKD) valuations separately. H-share often trades at a discount (AH溢价). Note the listing dates and IPO prices if relevant (e.g., H股破发情况).
  - **Cash flow and balance sheet**: The H-share cash flow statement may include items like foreign exchange impact (汇率影响) that do not appear in A-share reports. Reconcile if presenting cross-market analysis.
- **Recent M&A handling**: If the target company has acquired or merged with other listed companies within the last 1–2 years (e.g., 华润三九 acquiring 天士力 and 昆药集团), you MUST query each entity's financial data separately (target + each subsidiary). Consolidated reports may blend pre- and post-acquisition periods, making YoY comparisons misleading. When writing the Business Deep Dive (Section 2), create separate subsections for the parent and each major acquired entity, with standalone financials, strategic roles, and integration status. Note which data points are consolidated vs. pro-forma vs. standalone. In the risk section, flag goodwill impairment risk and integration execution risk.

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
- **Attribution discipline (critical):**
  - **Do not invent packaging phrases** that sound like research report quotes (e.g., "销售真空期", "阵痛期深水区"). Use plain descriptive language or quote the source directly with attribution.
  - **Do not reduce multi-factor causality to a single cause.** If a metric change (e.g., revenue drop) has multiple drivers (policy + channel reform + industry cycle), list all factors. Never say "X导致Y" when the reality is "X、Y、Z多重因素共振".
  - **When using company/management language, mark it as such.** Distinguish between: company原话 ("短期会有阵痛"), 媒体报道 ("终端覆盖出现暂时性断层"), and analyst包装 ("销售真空期"). Do not mix these without attribution.
  - **Data points require source tags.** Every quantitative claim (revenue, %, ranking) must have an inline source note on first mention.

### Step 4: Review and Deliver

**Output**: `./[stock code/ticker]_[company name]_深度研究报告_YYYYMMDD_HHmm.md`

Before delivering the report:
- Verify all stock codes and company names are accurate
- Check that each major section has substantive content (not just placeholder text)
- Ensure risk factors section is thorough and well-organized
- Confirm the report is self-contained and readable without external references
- Save the Markdown file to the current workspace root directory and present it to the user

### Step 5: Optional — Product Image Enhancement

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
