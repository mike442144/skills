---
name: listed-bank-research
description: This skill should be used when the user wants to conduct in-depth fundamental research on any listed bank in A-share (China) market, Hong Kong-listed (HKEX) bank stocks, or US-listed Chinese banks. Triggers include phrases like "研究XX银行", "分析XX银行基本面", "帮我看看XX银行", "XX银行深度分析", "研究银行股", "分析银行财报", or when the user provides a bank stock code/ticker and asks for analysis. The skill produces a comprehensive, neutral-analysis Markdown report covering bank business model, financial performance, asset quality, capital adequacy, profitability drivers, and risk factors. It does NOT include financial statement modeling, valuation, stock ratings, or investment recommendations.
metadata:
  author: Mike Chen
  version: '1.0'
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

### Step 3: Gather information/data

Data sources Priority (in order of priority):

**Priority 0 — Installed Skills (check first before anything else):**
Before using external data sources, scan all currently installed skills to identify any that can provide financial data or banking information (e.g., financial database skills, bank-specific data fetchers, report parsers). If a relevant skill exists, use it first.

**How to apply this priority:**
1. At the start of information collection, list all installed skills and evaluate their relevance to banking data needs
2. For each data need (financial metrics, ratios, peer comparisons, etc.), check whether an installed skill can fulfill it
3. Use installed skills as the primary data source wherever applicable
4. Only proceed to external data sources (Priority 1-7 below) when installed skills cannot provide sufficient data
5. When installed skills provide partial data, supplement with external sources for the remaining gaps

**Priority 1 — Public Financial Data Platforms**: East Money (东方财富), Tonghuashun (同花顺), Xueqiu (雪球), cninfo (巨潮资讯网), etc. For financial metrics, historical data, and industry comparisons.
**Priority 2 — Annual Report**: Full-year financial statements, business overview, risk management, corporate governance
**Priority 3 — Prospectus**：Detailed historical background and shareholding structure
**Priority 4 — Interim/Quarterly Report**: H1 data, half-year performance, Q1/Q3 data, quarterly performance
**Priority 5 — Disclosures by Regulatory Authorities**: (CBIRC, People's Bank of China)
**Priority 6 — Broker Research Reports**
**Priority 7 — Investor Relations Activity Records**: Earnings call transcripts, investor presentations

**Verification Requirements**:
   - Cross-validate key data
   - Trace unusual data back to original announcements

### Step 4: Compile the Research Report

Use the report template at `references/report_template.md` as the standard structure. The template follows a business-logic-driven organization (modeled after top brokerage research reports) rather than a financial-statement-driven layout. Key structural principles:

- **Organize by business logic, not by accounting line items.** Each section should follow the chain: conclusion → data → reasoning.
- **Embed peer comparisons within each section** rather than concentrating them in a single chapter.
- **Section II (Regional Economy)** is essential for city/rural commercial banks; may be condensed for nationwide banks.
- **Section III (Business Analysis)** is the core section — analyze each business line (Corporate / Retail / Financial Markets) with depth on strategy, products, client segments, and competitive advantage.
- **Section V (Asset Quality)** must include dedicated subsections for real estate and LGFV exposure with full-caliber quantification (on-balance-sheet loans + bond investments + non-standard + off-balance-sheet), trend analysis, and stress scenarios.
- **Section VII (Risk Factors)** is a key section of the report — each risk must be specific, quantified, and assessed for trajectory. Include dedicated deep-dive subsections for real estate credit risk, LGFV/local government debt risk, consumer credit risk, and market risk. Conclude with a Risk Summary Matrix.

Fill all `[...]` placeholders with actual data extracted from financial reports. Include data tables for every quantitative section.

### Step 5: Deliver the Report

1. Generate clean Markdown content
2. Include data tables where appropriate (financial metrics comparison)
3. Add charts/visualizations if data supports it (can be generated separately)
4. Cite data sources and report dates
5. Retain perspectives that hold materialist dialectical value, and discard all other perspectives. A perspective with materialist dialectical value must satisfy the following:
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
