# Multi-Year MD&A Systematic Review

> Produces structured inputs for report Sections 4.1 (Management View), 4.2 (Strategy), and 4.3 (Track Record).

## Why This Matters

A single year's MD&A tells you what management *says now*. Ten years of MD&A tell you what management *has consistently done* — which strategic threads are real, which were abandoned, and whether their explanations hold up over time. This is the single most powerful tool for assessing management credibility and strategic coherence.

## Step 1: Extract MD&A Text

Assumes the multi-year annual report PDFs are already downloaded per SKILL.md Priority 0 (cninfo for A-share, hkexnews for HKEX). If fewer than 5 years are available (recent IPO), do all available years and note the limitation.

Use PyMuPDF (`fitz`) to flatten each PDF to text (`"\n".join(page.get_text() for page in doc)`), then locate the MD&A section. The mechanics are simple, but layouts vary by company and year — no regex is universally safe. Three structural facts to keep in mind:

1. **The section header appears multiple times**: in the table of contents, at the actual section start, and often as cross-references inside later chapters ("详见'第三节 管理层讨论与分析'之……"). Do NOT blindly pick the first or last occurrence — use the one immediately followed by actual section content (a subsection heading like "一、报告期内……"). TOC entries are followed by dotted leaders and page numbers; cross-references sit mid-sentence.

2. **Section number and name both vary** across years and companies (第三/四节; 管理层/经营情况讨论与分析; occasional non-standard layouts). Match both name variants broadly; if the chapter header is not found at all, fall back to searching for the subsection marker directly.

3. **The section ends at the next major chapter** (公司治理 / 重要事项 etc.). Search for it starting well past the section's own beginning so you don't hit the TOC, and expect layout quirks (e.g. no space between section number and name).

**Verify every extraction before using it.** A healthy MD&A is roughly 15,000–65,000 characters; anything under ~1,000 almost certainly hit the TOC or a cross-reference — try the next candidate occurrence, or read the PDF's actual section boundaries and adapt. When a pattern fails, adjust it to the specific PDF rather than stacking more fallback rules.

Store the extracted text per year for cross-year analysis.

## Step 2: Four-Dimension Cross-Year Analysis

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

## Step 3: Synthesize Into Report Inputs

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
4. **Section number variation**: Only 2015 A-share reports use 第四节 for MD&A; 2016+ typically use 第三节, with occasional company-specific variants. Be aware when spot-checking.
5. **HKEX reports**: HK-listed company MD&A (under IFRS) is typically less structured than A-share. Supplement with earnings call transcripts and investor presentations for management narrative.
