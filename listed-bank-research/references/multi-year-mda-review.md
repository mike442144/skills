# Multi-Year MD&A Systematic Review (Bank)

> Default step in all bank research (no minimum listing period). Produces structured inputs for report Sections I.4 (Strategic Positioning), IV.1 (NIM Narrative), V.1 (Asset Quality Narrative vs Reality), VII (Risk Factors), and VIII (Management Credibility Assessment).

## Why This Matters for Banks

Bank annual report MD&A is especially rich — it contains management's narrative on NIM drivers, asset quality migration, strategic positioning (零售转型, 财富管理, 对公专业化), and risk outlook. A single year tells you the current story; 10 years reveal whether the strategy was executed or merely re-announced, whether NIM management claims hold up, and whether asset quality warnings were timely or retrospective.

Bank MD&A differs from general companies in two ways: (1) it is more quantitative (rates, ratios, structure breakdowns embedded in the narrative), and (2) it carries regulatory-mandated disclosures that are consistent year-over-year, making cross-year comparison more structured.

## Step 1: Download Annual Reports

Same as general companies:
- **A-share banks**: `node ~/Projects/tinyant/cninfo/index.js --codes <6-digit> --annual --year <start-end>`
- **HKEX banks**: `node ~/Projects/tinyant/hkexnews/index.js --codes <5-digit> --year <start-end>`

## Step 2: Extract MD&A Text

Use the extraction method in `listed-company-research/references/cninfo-annual-report-extraction.md` (PyMuPDF + regex). Bank MD&A sections are typically longer (20,000-70,000 chars) due to mandatory risk disclosures.

## Step 3: Four-Dimension Cross-Year Analysis

### 3.1 Strategic Positioning Timeline

Track how the bank describes itself over time:

- **Stated strategy / positioning** (e.g., "零售之王", "同业之王", "最佳零售银行", "区域首选银行")
- **Strategic initiatives announced** (零售转型, 财富管理升级, 数字化转型, 对公专业化, 绿色金融)
- **Evolution**: rebranded, escalated, de-emphasized, or quietly abandoned

| Year | Stated Positioning | Key Strategic Initiatives | Prior Initiative Status |
|------|-------------------|-------------------------|------------------------|
| 20XX | [self-description] | [initiative + target] | [ongoing / completed / abandoned] |

Feeds Section I.4 (Strategic Positioning) and provides the basis for credibility assessment.

### 3.2 NIM & Profitability Narrative Tracking

Extract management's NIM explanation year-by-year:

- **Attribution**: What factors does management cite for NIM changes? (LPR cuts, deposit cost rigidity, loan repricing, asset structure optimization, etc.)
- **Forward-looking statements**: "息差有望企稳", "预计净息差将保持稳定", etc.
- **Actual outcome**: Did NIM stabilize as predicted? How quickly?

| Year | NIM (%) | Management Attribution (up/down) | Forward Statement | Next Year Actual |
|------|---------|----------------------------------|-------------------|-----------------|
| 20XX | [...] | [factors cited] | [guidance if any] | [actual NIM next year] |

This directly feeds Section IV.1 (NIM Deep Dive) narrative and tests management's forecasting ability on the most-watched bank metric.

### 3.3 Asset Quality Narrative vs Reality

Track management's asset quality commentary:

- **Risk warnings**: Did management flag sector risks (real estate, LGFV, retail credit) before or after deterioration showed in the numbers?
- **Confidence statements**: "资产质量总体可控", "不良率预计保持低位" — were these accurate?
- **Write-off / disposal narrative**: How does management frame aggressive write-offs? (Proactive risk resolution vs hiding problems)

| Year | NPL (%) | Management Tone | Sector Warnings Issued | Subsequent Reality |
|------|---------|----------------|----------------------|-------------------|
| 20XX | [...] | [confident / cautious / defensive] | [sectors flagged] | [what happened next] |

Feeds Section V (Asset Quality) and Section VII.2/7.3 (RE/LGFV risk).

### 3.4 Guidance vs Actuals

Same as the general company version — extract explicit forward-looking guidance and track outcomes:

| Year Guided | Statement | Metric | Actual Outcome | Verdict |
|-------------|-----------|--------|---------------|---------|
| 20XX | "[quote]" | [NIM/NPL/ROE/growth] | [actual] | ✅/⚠️/❌ |

Bank-specific guidance categories:
- NIM guidance ("息差有望企稳" → did it?)
- Asset quality guidance ("不良率将保持稳定" → did it?)
- Growth targets (loan/deposit/AUM growth targets)
- Capital management (CAR targets, fundraising plans)

## Step 4: Synthesize Into Report Inputs

| Analysis Dimension | Feeds Report Section | Output Format |
|-------------------|---------------------|---------------|
| 3.1 Strategic Timeline | §I.4 (Strategic Positioning) | Year-by-year positioning table |
| 3.2 NIM Narrative | §IV.1 (NIM Deep Dive) | NIM attribution + guidance tracking table |
| 3.3 Asset Quality Narrative | §V (Asset Quality) + §VII (Risk) | Narrative + warning-timing analysis |
| 3.4 Guidance vs Actuals | §VIII (Summary) credibility assessment | Scorecard table |

## Pitfalls

1. **Regulatory language vs genuine guidance**: Bank MD&A contains boilerplate risk disclosures mandated by regulators. Distinguish formulaic language ("本行持续加强风险管理") from genuine forward-looking statements with specific targets or timelines.
2. **Restatement effects**: Bank financials are frequently restated (especially after accounting standard changes like IFRS 9). When comparing MD&A narratives across years, verify that the metrics cited are on a consistent basis.
3. **Interim vs annual**: Bank management commentary in interim reports can be more candid than annual reports (less polished). Supplement with H-share interim reports if available.
4. **HKEX bank reports**: HK-listed Chinese banks' MD&A is shorter and less structured. The A-share version (if dual-listed) is always preferred for MD&A analysis. Use the H-share version only for pure HK-listed banks.
