# Multi-Year MD&A Systematic Review (Bank)

> Default step in all bank research (no minimum listing period). Produces structured inputs for report Sections I.4 (Strategic Positioning), IV.1 (NIM Narrative), V.1 (Asset Quality Narrative vs Reality), VII (Risk Factors), and VIII (Management Credibility Assessment).

## Why This Matters for Banks

Bank annual report MD&A is especially rich — it contains management's narrative on NIM drivers, asset quality migration, strategic positioning (零售转型, 财富管理, 对公专业化), and risk outlook. A single year tells you the current story; 10 years reveal whether the strategy was executed or merely re-announced, whether NIM management claims hold up, and whether asset quality warnings were timely or retrospective.

Bank MD&A differs from general companies in two ways: (1) it is more quantitative (rates, ratios, structure breakdowns embedded in the narrative), and (2) it carries regulatory-mandated disclosures that are consistent year-over-year, making cross-year comparison more structured.

## Step 1: Download Annual Reports

Download the latest 10 years of annual reports (or since listing if listed < 10 years):
- **A-share banks**: `node ~/Projects/tinyant/cninfo/index.js --codes <6-digit> --annual --year <start-end>`
- **HKEX banks**: `node ~/Projects/tinyant/hkexnews/index.js --codes <5-digit> --year <start-end>`

## Step 2: Source Management Narrative

Two complementary sources are needed — annual report MD&A text for structured disclosures, and 业绩说明会 transcripts for candid forward-looking statements.

### 2a. Annual Report MD&A Text

Use the extraction method in `listed-company-research/references/cninfo-annual-report-extraction.md` (PyMuPDF + regex). Bank MD&A sections are typically longer (20,000-70,000 chars) due to mandatory risk disclosures. Focus on 董事长致辞, 行长致辞, and 经营分析 sections.

### 2b. 业绩说明会 Transcripts (PRIMARY source for forward-looking quotes)

Annual report MD&A text is polished and formulaic. 业绩说明会 (earnings briefing) transcripts contain Q&A where management gives more candid directional predictions, NIM attribution, and risk commentary. These are the richest source for dimensions 3.2 (NIM Narrative) and 3.4 (Strategic Outlook vs Reality).

**Sourcing priority:**
1. **SSE/SZSE investor relations platform** — official 业绩说明会 transcripts (e.g., `vrs.sse.com.cn`). Search: `"{bank} {year}年度业绩说明会"` → look for `上证路演中心` or `e互动` pages
2. **Bank's own IR page** — some banks host transcripts on their investor relations section
3. **Authoritative financial media summaries** — 财联社 (`cls.cn`), 21世纪经济报道 (`21jingji.com`), 经济观察报 — these report key quotes from 业绩说明会 with attribution
4. **券商研报** — 国信/中信/中泰等券商在业绩点评中常引用管理层原话

**Search query templates:**
- `"{bank} {year}年度业绩说明会 净息差"` → NIM attribution + forward statement
- `"{bank} {year}年度业绩说明会 资产质量"` → risk warnings + confidence statements
- `"{bank} {year}年报 董事长致辞 展望"` → strategic outlook

**Attribution rule:** When quoting management, always tag the source type: `(2023年度业绩说明会·行长XXX·2024-05-15)` or `(2024年报董事长致辞)`. If a quote is obtained indirectly (via media summary rather than primary transcript), note: `据{媒体}对{year}年业绩说明会报道`. If a specific management quote cannot be found, write `[未找到原文]` — never fabricate.

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

### 3.4 Strategic Outlook (展望) vs Reality

Chinese banks rarely give formal numerical guidance (unlike US companies). Instead, their MD&A 展望 sections contain forward-looking statements about strategic direction, market outlook, and directional predictions. Track these and check if they were borne out:

- **Strategic direction**: "推进零售转型", "深化数字化转型", "拓展成渝地区市场"
- **Directional predictions**: "息差有望企稳", "不良率保持稳定", "营收平稳增长"
- **Risk outlook**: "密切关注房地产/LGFV风险" — was the warning timely?
- **Semi-quantitative targets** (rare): "力争不良率控制在X%以内", "资本充足率不低于X%"

| Year | Outlook Statement | Type | Actual Outcome | Verdict |
|------|------------------|------|---------------|---------|
| 20XX | "[quote from 展望]" | [战略方向/指标方向/风险展望] | [what happened] | ✅/⚠️/❌ |

Distinguish formulaic regulatory boilerplate ("本行持续加强风险管理") from genuine forward-looking statements with specific direction or targets. Feeds Section VIII (Management Credibility Assessment).

## Step 4: Synthesize Into Report Inputs

| Analysis Dimension | Feeds Report Section | Output Format |
|-------------------|---------------------|---------------|
| 3.1 Strategic Timeline | §I.4 (Strategic Positioning) | Year-by-year positioning table |
| 3.2 NIM Narrative | §IV.1 (NIM Deep Dive) | NIM attribution + forward statement tracking table |
| 3.3 Asset Quality Narrative | §V.1 (Asset Quality) + §VII (Risk) | Narrative + warning-timing analysis (see Pitfall #5: distinguish 滞后确认 from 前瞻预警) |
| 3.4 Strategic Outlook vs Reality | §VIII (Management Credibility Assessment) | Outlook vs Reality scorecard (see Pitfall #7: rate "低目标超完成" as ✅ but flag as conservative style) |

## Pitfalls

1. **Regulatory language vs genuine outlook**: Bank MD&A contains boilerplate risk disclosures mandated by regulators. Distinguish formulaic language ("本行持续加强风险管理") from genuine forward-looking statements with specific direction or targets.
2. **Restatement effects**: Bank financials are frequently restated (especially after accounting standard changes like IFRS 9). When comparing MD&A narratives across years, verify that the metrics cited are on a consistent basis.
3. **Interim vs annual**: Bank management commentary in interim reports can be more candid than annual reports (less polished). Supplement with H-share interim reports if available.
4. **HKEX bank reports**: HK-listed Chinese banks' MD&A is shorter and less structured. The A-share version (if dual-listed) is always preferred for MD&A analysis. Use the H-share version only for pure HK-listed banks.
5. **"滞后确认" vs 前瞻预警**: In practice, management risk warnings tend to *confirm* already-happened deterioration rather than *predict* future problems. For example, 杭州银行 management called real estate risk "总体可控" in 2023, but 对公房地产不良率 was already rising; by 2024 they acknowledged "信用风险管理压力上升" only after 关注类迁徙率 had already surged. When populating the 3.3 table, distinguish between warnings issued *before* deterioration vs. those issued *after* — the former are genuine 前瞻预警, the latter are 滞后确认. This distinction is the core analytical value of the multi-year review.
6. **Selective disclosure on risk concentrations**: Management may flag certain risks (房地产, 化债) in MD&A while omitting others — particularly LGFV/政信类 concentration, which for some city commercial banks can be 40-50% of total loans. In 成都银行's case, ~50% of loans are政信/基建-related, yet历年MD&A never explicitly named this as a risk concentration. When analyzing dimension 3.3, cross-reference the bank's actual sector exposure (from F9 银行贷款结构) against the sectors management *chose to flag* — the gap between the two is itself a material finding.
7. **"低目标超完成" guidance style**: Some banks deliberately set conservative semi-quantitative targets that are easily exceeded (e.g., 杭州银行 2024 set 2025 asset growth target at 6.1%, actual 11.96%). This is not a forecasting error — it's an intentional expectation-management strategy. When assessing credibility in dimension 3.4, "low target + over-delivery" should be rated ✅ but noted as conservative guidance style, not as evidence of superior forecasting ability.
