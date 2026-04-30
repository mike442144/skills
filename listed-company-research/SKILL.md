---
name: listed-company-research
description: "This skill should be used when the user wants to conduct in-depth fundamental research on any listed company, including A-share (China), Hong Kong-listed (HKEX), or US-listed (NYSE/NASDAQ) stocks. Triggers include phrases like '研究XX公司', '分析XX', 'XX公司基本面', '帮我看看XX', 'deep dive XX', 'research XX company', '管理层展望', '战略规划', 'management strategy', 'outlook', or when the user provides a stock code/ticker and asks for analysis. The skill produces a comprehensive, neutral-analysis Markdown report covering company overview, detailed business decomposition, industry competitive landscape, management outlook & strategy, and risk factors. It does NOT include financial statement modeling, valuation, stock ratings, or investment recommendations."
metadata:
  author: Mike Chen
  version: '1.2'
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
- **Anti-hallucination**: Never fabricate data or use the model's own knowledge to fill in quantitative claims. If a figure is not found, explicitly note the gap rather than inventing a number. All quantitative data must come from verifiable sources with attribution. Model knowledge may only be used for qualitative context, never as a substitute for sourced data

### Step 3: Write the Research Report

Structure the report following the template in `references/report-template.md`. Load this file for the full template with detailed section-by-section guidance.

**Key writing principles:**
- Use simple, plain language to explain business models — imagine explaining to an intelligent non-specialist
- For each business segment, clearly explain: what it is, who the customers are, what inputs/resources are needed, how revenue is generated, and the business flow/process
- Support all factual claims with specific data points and cite sources
- Maintain a neutral, objective tone throughout — present facts and analysis, not opinions or recommendations
- Highlight information gaps transparently
- Use Chinese (中文) as the default output language unless the user requests otherwise

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
