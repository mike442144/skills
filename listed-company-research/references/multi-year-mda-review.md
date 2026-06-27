# Multi-Year MD&A Systematic Review

> Produces structured inputs for report Sections 4.1 (Management View), 4.2 (Strategy), and 4.3 (Track Record).

## Why This Matters

A single year's MD&A tells you what management *says now*. Ten years of MD&A tell you what management *has consistently done* — which strategic threads are real, which were abandoned, and whether their explanations hold up over time. This is the single most powerful tool for assessing management credibility and strategic coherence.

## Step 1: Download Annual Reports

Download the most recent ~10 years of annual report PDFs:

- **A-share**: `cd ~/Projects/tinyant/cninfo && node index.js --codes <6-digit> --annual --year <start-end>`
- **HKEX**: `cd ~/Projects/tinyant/hkexnews && node index.js --codes <5-digit> --year <start-end>`

If fewer than 5 years available (recent IPO), do all available years and note the limitation.

## Step 2: Extract MD&A Text

Use the extraction method in `cninfo-annual-report-extraction.md` (PyMuPDF + regex, last-match logic, end-marker detection). That reference handles the PDF mechanics and known pitfalls (TOC vs content, section number variation 2015 vs 2016+).

Expected yield: 15,000-65,000 chars per year. Anything under 1,000 = failed extraction (hit TOC).

Store extracted text per year for cross-year analysis.

## Step 3: Four-Dimension Cross-Year Analysis

Process all extracted MD&A texts through these four analytical lenses:

### 3.1 Strategic Timeline

For each year, extract:
- **New initiatives announced** (with management's stated timeline/targets)
- **Status of previously announced initiatives** (continued / evolved / quietly dropped)
- **Strategic pivots or repositioning** (renaming, reframing, de-emphasizing)

Build a year-by-year table:

| Year | New Initiatives Announced | Prior Initiatives Status | Strategic Repositioning |
|------|--------------------------|-------------------------|------------------------|
| 20XX | [initiative, target, timeline] | [ongoing / completed / abandoned] | [shift in framing] |

This table directly feeds Section 4.3 (Track Record). Each row is a candidate for the Credibility Scorecard — did they do what they said?

### 3.2 Theme Tracking

Select 3-5 themes most relevant to this company's investment thesis (not generic). Examples by company type:

- **Export manufacturer**: 海外/出口/KD/属地化/品牌出海
- **Platform/fintech**: 轻资产/科技赋能/合规/降本增效
- **Cyclical industrial**: 产能利用率/产品结构升级/成本管控/周期应对
- **Consumer brand**: 渠道下沉/品牌升级/产品高端化/线上化

For each theme, extract relevant paragraphs year-by-year. Track:
- When the theme first appears (emergence)
- How management's framing evolves (escalation → peak → de-emphasis → disappearance)
- Whether narrative aligns with financial outcomes (e.g., "海外突破" claims vs actual export revenue)

Output: themed excerpts organized by year, suitable for narrative paragraphs in Sections 4.1/4.2.

### 3.3 Management Attribution Pattern

Analyze how management explains good vs bad years:

- **Good years**: Does management credit strategy/skill (internal) or favorable market (external)?
- **Bad years**: Does management blame external factors (macro/policy/industry) or acknowledge execution gaps?
- **Consistency**: Are the same factors cited as tailwinds in good years and headwinds in bad years? (Self-serving bias indicator)

Pattern: consistent internal attribution for success + external attribution for failure = low self-awareness or intentional framing.

Feed into Section 4.1 "Attribution analysis for recent performance."

### 3.4 Forward Guidance vs Actuals

Extract explicit forward-looking statements (guidance, targets, "预计/力争/计划"):

| Year Guided | Statement | Timeline | Actual Outcome (from later years' MD&A) | Verdict |
|-------------|-----------|----------|----------------------------------------|---------|
| 20XX | "[quote]" | [year] | [what actually happened] | ✅/⚠️/❌ |

Verdict criteria:
- ✅ Achieved: met or exceeded the stated target within the stated timeframe
- ⚠️ Partial: meaningfully progress but fell short, OR achieved but delayed 1+ year
- ❌ Missed: abandoned, or outcome contradicts the guidance without acknowledgment

This is the core input for the Section 4.3 Credibility Scorecard. Aim for 5-10 entries covering the full period.

## Step 4: Synthesize Into Report Inputs

The four dimensions produce structured material that maps directly to report sections:

| Analysis Dimension | Feeds Report Section | Output Format |
|-------------------|---------------------|---------------|
| 3.1 Strategic Timeline | §4.2 (Strategy) + §4.3 (Track Record) | Year-by-year initiative table |
| 3.2 Theme Tracking | §4.1 (Management View) + §4.2 (Strategy) | Narrative paragraphs with year citations |
| 3.3 Attribution Pattern | §4.1 (Management View) | 2-3 paragraph analytical narrative |
| 3.4 Guidance vs Actuals | §4.3 (Track Record) | Credibility Scorecard table |

**Do not dump raw extracted text into the report.** The output should be synthesized analysis — the MD&A review is the research back-end, the report is the front-end.

## Pitfalls

1. **Extraction coverage**: Verify each year's extraction succeeded (char count check). A single failed year creates a gap in the timeline.
2. **Narrative inflation**: Management language tends to inflate over time. Compare claims against financial data, not against management's own prior claims.
3. **Selective quoting**: When building the Credibility Scorecard, include both hits and misses. Confirmation bias (only quoting failures) undermines credibility of the analysis itself.
4. **Section number variation**: Only 2015 A-share reports use 第四节 for MD&A; 2016+ use 第三节. The extraction regex handles both, but be aware when spot-checking.
5. **HKEX reports**: HK-listed company MD&A (under IFRS) is typically less structured than A-share. Supplement with earnings call transcripts and investor presentations for management narrative.
