---
name: listed-company-research
description: "This skill should be used when the user wants to conduct in-depth fundamental research on any listed company, including A-share (China), Hong Kong-listed (HKEX), or US-listed (NYSE/NASDAQ) stocks. Triggers include phrases like '研究XX公司', '分析XX', 'XX公司基本面', '帮我看看XX', 'deep dive XX', 'research XX company', or when the user provides a stock code/ticker and asks for analysis. The skill produces a comprehensive, neutral-analysis Markdown report covering company overview, detailed business decomposition, industry competitive landscape, and risk factors. It does NOT include financial statement modeling, valuation, stock ratings, or investment recommendations."
---

# Listed Company Fundamental Research

## Overview

Conduct structured, in-depth fundamental research on any publicly listed company across A-share, HKEX, and US markets. The output is a comprehensive Markdown research report with a neutral, analytical tone — no stock ratings, no target prices, no investment recommendations.

## Workflow

### Step 1: Identify the Target Company

Parse the user's input to identify the target company:
- Extract company name, stock code, ticker, or any identifiable reference
- Determine the listing market (A-share / HKEX / US) based on the stock code format:
  - A-share: 6xxxxx (Shanghai), 0xxxxx (Shenzhen Main), 3xxxxx (ChiNext), 688xxx (STAR)
  - HKEX: 0xxxxx or 8xxxxx or 2xxxxx (usually with .HK suffix)
  - US: alphabetic ticker (e.g., AAPL, TSLA, BABA)
- If ambiguous, ask the user to clarify which company or market

### Step 2: Collect Information

Gather as much publicly available information as possible. **Always follow this priority order:**

**Priority 0 — Installed Skills (check first before anything else):**
Before initiating any web search, scan all currently installed skills to identify any that are relevant to data collection for this research task. If a relevant skill exists, use it first to obtain data. Examples include but are not limited to:
- Skills that provide access to financial databases (e.g., stock data, company profiles)
- Skills that can fetch or parse specific document formats (e.g., PDF readers for annual reports)
- Skills that enable browser automation for accessing data platforms
- Any other skill whose capabilities overlap with the information needs of this research

**How to apply this priority:**
1. At the start of information collection, list all installed skills and evaluate their relevance
2. For each data need (company info, financials, industry data, etc.), check whether an installed skill can fulfill it
3. Use installed skills as the primary data source wherever applicable
4. Only proceed to web search (Priority 1-4 below) when installed skills cannot provide sufficient data
5. When installed skills provide partial data, supplement with web search for the remaining gaps

**Priority 1 — Company Primary Sources (web search, highest priority when skills are insufficient):**
- Latest annual report / 20-F / 6-K filing
- Latest prospectus (if newly listed)
- Company official website (about page, product/service pages, investor relations)
- ESG/CSR reports (for risk and governance information)
- Company earnings call transcripts

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

**Information collection strategy:**
- Conduct multiple rounds of targeted searches to cover all aspects of the research framework
- For each search, extract key facts, figures, and quotes; note the source
- Prioritize primary sources over secondary; prefer recent data over older data
- When sources conflict, present both perspectives with attribution
- If information is unavailable for a section, explicitly note the gap rather than omitting the section
- **Track which skills were used:** Maintain a running list of every installed skill invoked during research (skill name, what data it provided, and for which report section). This list must be included in the report's "Sources & Limitations" section

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

Before delivering the report:
- Verify all stock codes and company names are accurate
- Check that each major section has substantive content (not just placeholder text)
- Ensure risk factors section is thorough and well-organized
- Confirm the report is self-contained and readable without external references
- Deliver the Markdown file and present it to the user

## Research Framework Summary

The report covers four core pillars (in order):

1. **Company Overview** — Basic profile, listing info, market position summary
2. **Business Deep Dive** — Detailed breakdown of each business segment with plain-language explanations of business models, customers, revenue drivers, and business processes
3. **Industry & Competitive Landscape** — Industry overview, value chain, market size, competitive dynamics, company positioning
4. **Risk Factors** — Systematic identification and analysis of business, financial, industry, regulatory, and other risks

**Explicitly excluded:** Financial statement line-by-line analysis, valuation modeling (DCF, multiples), stock ratings, target prices, investment recommendations.

## Resources

### references/
- `report-template.md` — Full report template with detailed section-by-section writing guidance and formatting instructions. Load this file when writing the report.
