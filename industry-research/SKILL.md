---
name: industry-research
description: >
  This skill should be used when the user wants to conduct in-depth research on any industry,
  including traditional industries (manufacturing, retail, finance, etc.) and emerging industries
  (AI, new energy, biotech, etc.). Triggers include phrases like "研究XX行业", "分析XX产业",
  "XX行业深度分析", "行业研究报告", "industry research", "market analysis", or when the user
  asks for industry overview, market size, competitive landscape, value chain analysis, or
  investment opportunities in a specific sector. Produces a comprehensive, data-driven Markdown
  research report covering industry definition, market size and growth, value chain analysis,
  competitive landscape, policy environment, technology trends, and investment opportunities
  with risks.
metadata:
  author: Mike Chen
  version: '1.0'
---

# Industry Research Skill

## Purpose

This skill enables comprehensive, data-driven research on any industry. It produces structured Markdown reports that cover all critical dimensions of industry analysis, from market sizing to competitive dynamics to investment implications.

## When to Use

- User requests industry analysis or research (e.g., "研究新能源汽车行业", "分析AI产业")
- User asks for market size, growth trends, or industry overview
- User needs competitive landscape or value chain analysis
- User wants to understand policy environment or regulatory impact on an industry
- User seeks investment opportunities or risk assessment in a sector

## Research Workflow

### Step 1: Define Industry Scope

Before starting research, clarify:
- **Industry definition**: What exactly does this industry encompass?
- **Sub-segments**: What are the main sub-sectors or categories?
- **Geographic scope**: Global, China-specific, or regional?
- **Time horizon**: Historical analysis period and forecast period

If the user doesn't specify, default to:
- China market focus (with global context where relevant)
- Historical: past 3-5 years
- Forecast: next 3-5 years

### Step 2: Gather Data

Use multiple sources to ensure comprehensive and accurate research:

1. **Web search** (`web_search` tool):
   - Search for industry reports, market research, and analyst opinions
   - Use Chinese keywords for China industries (e.g., "XX行业 市场规模 2024")
   - Use English keywords for global context (e.g., "XX industry market size 2024")
   - Search for recent news, policy changes, and industry developments

2. **Government and official statistics**:
   - National Bureau of Statistics (国家统计局)
   - Industry associations and ministries
   - Customs data, production data, import/export statistics

3. **Listed company data**:
   - Research key players in the industry using `listed-company-research` skill if needed
   - Analyze revenue, profit margins, market share of major companies
   - Use annual reports and earnings calls as industry data sources

4. **Third-party research**:
   - Consulting firms (McKinsey, BCG, Deloitte, etc.)
   - Industry-specific research institutions
   - Financial institution reports (brokerage research)

### Step 3: Structure the Report

Use the template in `assets/industry-report-template.md` as the foundation. The report must include:

1. **Executive Summary** (执行摘要)
   - Key findings in 3-5 bullet points
   - Industry attractiveness assessment
   - Top 2-3 investment themes or risks

2. **Industry Overview** (行业概况)
   - Definition and scope
   - Industry classification and sub-segments
   - Key characteristics and business models

3. **Market Size and Growth** (市场规模与增长)
   - Historical market size (past 3-5 years)
   - Current market size with specific data points
   - Growth rate trends (CAGR)
   - Future projections with assumptions
   - Include tables and/or charts where possible

4. **Value Chain Analysis** (产业链分析)
   - Upstream suppliers and inputs
   - Midstream industry participants
   - Downstream customers and distribution channels
   - Value distribution across the chain
   - Key bottlenecks or moats

5. **Competitive Landscape** (竞争格局)
   - Market concentration (CR3, CR5, HHI if available)
   - Top players and market shares
   - Competitive dynamics and barriers to entry
   - Key success factors
   - Include a competitive positioning table

6. **Policy and Regulatory Environment** (政策与监管环境)
   - Current policies and regulations
   - Recent policy changes and impact
   - Government support or restrictions
   - Regulatory trends and outlook

7. **Technology and Innovation Trends** (技术与创新趋势)
   - Current technology landscape
   - Emerging technologies and disruption potential
   - R&D investment trends
   - Technology adoption curves

8. **Investment Opportunities and Risks** (投资机会与风险)
   - Key investment themes and logic
   - Attractive sub-segments
   - Major risks (policy, technology, competition, macro)
   - Risk-reward assessment

9. **Key Players** (重点企业)
   - Table of top 5-10 companies with key metrics
   - Brief profiles of each
   - Cross-reference to detailed company research if available

10. **Appendix** (附录)
    - Data sources
    - Methodology notes
    - Glossary of industry terms

### Step 4: Write the Report

Follow these guidelines:

- **Data-driven**: Every claim should be backed by data where possible
- **Neutral analysis**: Present facts and multiple perspectives, avoid investment recommendations
- **Clear structure**: Use headings, subheadings, tables, and bullet points
- **Cite sources**: Note data sources inline (e.g., "据国家统计局数据,2024年...")
- **Quantify**: Use specific numbers, percentages, and ranges instead of vague terms
- **Visual**: Include markdown tables for comparisons; suggest chart types where appropriate
- **Language**: Match user's language (Chinese by default for China industries)

### Step 5: Review and Refine

Before delivering:
- Verify all data points are from credible sources
- Check for logical consistency throughout the report
- Ensure all major sections are covered
- Confirm the report is neutral and does not include stock ratings or price targets
- Add a disclaimer that this is research, not investment advice

## Resources

- `assets/industry-report-template.md`: Markdown template for industry research reports
- `references/industry-data-sources.md`: Comprehensive list of industry data sources and how to access them

## Important Notes

- This skill produces **research reports only**, not investment recommendations
- Do NOT include stock ratings, price targets, or "buy/sell" recommendations
- Always maintain analytical neutrality
- Clearly distinguish between facts/data and analysis/opinions
- Flag uncertainties and data limitations explicitly
