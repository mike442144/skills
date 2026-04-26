---
name: research-report-generator
description: >
  This skill should be used when the user wants to produce a structured company research report,
  industry research report, or any analytical report based on a collection of local research
  materials (documents, spreadsheets, PDFs, etc.). Triggers include phrases like "公司研究报告",
  "研究报告", "research report", "write a report based on these materials", "帮我写个报告",
  "根据资料写报告", "分析这些资料", "基于这些文件做研究", "做个研究", "出个报告",
  "company research", "deep dive report", or when the user provides a folder of research
  materials and asks for structured analysis. This skill orchestrates a 6-phase workflow:
  material ingestion → outline confirmation → execution planning → report writing →
  executive summary → data verification checklist.
---

# Research Report Generator

Generate structured, data-driven research reports from a collection of local research materials.

## Core Principles

1. **Data primacy**: Always prioritize data from user-provided materials. Only supplement with web searches when confirmed absent from the source materials. All supplementary data must cite reputable media sources.
2. **Conclusion-first writing**: Every chapter opens with key findings, then expands with supporting evidence and data.
3. **Source attribution**: Every data point must cite its origin — file name, sheet name, row/column, or expert name from interview transcripts.
4. **Neutral stance**: Maintain objective, balanced analysis. Present bull and bear cases where applicable.
5. **Concise rigor**: Write tight, professional prose. No filler, no hedging without substance.

## Workflow

Execute the following 6 phases in strict order. Do NOT skip phases or combine them without user approval.

### Phase 1: Material Ingestion & Overview

**Goal**: Read and understand all research materials, then present a structured overview to the user.

**Trigger**: Ask the user for the local or network folder path containing their research materials. If the user has already provided it, proceed directly.

**Information Retrieval Strategy**:

Adapt the retrieval approach based on material volume and complexity:

- **File discovery**: List all files in the provided folder. Identify file types (.xlsx, .docx, .pdf, .csv, .md, .txt, .pptx, etc.).
- **Spreadsheet files (.xlsx/.csv)**: For each file, list all sheet names, row/column dimensions, and column headers. Read the first 5-10 rows of each sheet to understand the data structure. For large sheets (>100 rows), sample strategically (first rows, last rows, key sections).
- **Document files (.docx/.pdf/.md/.txt)**: Convert to readable format if needed (use pandoc for .docx). Read the full content for short documents (<500 lines). For long documents, first scan for structural markers (headings, sections), then read strategically by section.
- **Large file handling**: For files with >10,000 lines or >50 sheets, create a structural index first (headings, sheet names, key metrics), then deep-read specific sections as needed in later phases.
- **Encoding awareness**: Handle CJK (Chinese/Japanese/Korean) content properly. Use UTF-8 encoding for all reads.

**Output**: Present a structured overview table to the user:

```
| # | File Name | Type | Content Summary | Key Data Points |
|---|-----------|------|-----------------|-----------------|
| 1 | ...       | xlsx | ...             | ...             |
```

Include a brief narrative summary of what the materials collectively cover (themes, time periods, data types, expert sources, etc.).

### Phase 2: Outline & Language Confirmation

**Goal**: Collect the user's research questions and preferred output language.

**Action**: Stop and ask the user two questions:

1. **Research outline or question list**: What specific questions or topics should each chapter address? Accept numbered lists, bullet points, or free-form descriptions.
2. **Report language**: What language should the report be written in? Default to Chinese (中文) if not specified.

Wait for the user's response before proceeding. Do NOT assume or generate the outline independently.

### Phase 3: Execution Planning

**Goal**: Design the optimal execution strategy based on the outline complexity and material coverage.

**Planning decisions**:

1. **Chapter-material mapping**: For each chapter/question, identify which specific files, sheets, and sections contain relevant data. This narrows the retrieval scope for each chapter.

2. **Parallelization strategy**: Evaluate whether to use subagents for parallel execution:
   - **Sequential (default)**: Use when chapters are interdependent or the material set is small (<5 files).
   - **Parallel by chapter**: Use when chapters are independent and the material set is large. Assign one subagent per chapter with its specific material subset.
   - **Parallel within chapter**: Use when a single chapter requires data from many independent sources (e.g., cross-referencing 10+ expert interviews).
   - When using subagents, provide each with: (a) the specific chapter questions, (b) the exact file paths and sheet names to read, (c) the writing guidelines from this skill, (d) the report language.

3. **Data gap identification**: Flag any questions that may not be fully answerable from the provided materials. Plan web search strategies for these gaps.

4. **Retrieval plan per chapter**: For each chapter, specify:
   - Primary data sources (file → sheet → relevant rows/columns)
   - Secondary data sources (other files for cross-validation)
   - Potential web search topics (if gaps identified)

**Output**: Present the execution plan to the user as a structured table:

```
| Chapter | Key Questions | Primary Sources | Secondary Sources | Gap Filling | Execution Mode |
|---------|--------------|-----------------|-------------------|-------------|----------------|
| 1       | ...          | File A (Sheet X)| File B (Sheet Y)  | Web: topic  | Sequential     |
```

Wait for user confirmation before proceeding to Phase 4.

### Phase 4: Report Writing

**Goal**: Execute the plan and produce the full report.

**File naming**:
- Chinese reports: `关于[主题]的研究报告.md`
- English reports: `[Topic]_Research_Report.md`

**Writing guidelines**:

1. **Structure**: Each chapter corresponds one-to-one with the user's outline/questions.
2. **Conclusion-first**: Every chapter begins with a bolded key finding summary (2-4 bullet points), followed by detailed analysis.
3. **Data citation format**:
   - Spreadsheet data: `（来源：[文件名], Sheet "[工作表名]"）` or `(Source: [filename], Sheet "[sheet name]")`
   - Interview/document data: `（来源：[文件名], [专家名/章节名]）` or `(Source: [filename], [expert/section])`
   - Web data: `（来源：[媒体名], [日期], [URL if available]）` or `(Source: [media name], [date], [URL])`
4. **Tables over prose**: When presenting comparative data, financial metrics, or multi-dimensional information, use Markdown tables.
5. **Quantitative precision**: Include specific numbers, percentages, and time periods. Avoid vague language like "significant increase" without backing numbers.
6. **Cross-validation**: When multiple sources provide the same metric, note any discrepancies and explain which figure is used and why.
7. **Page-level discipline** (for multi-page outputs like PDF/PPT): Each logical section should follow the one-page-one-point principle — a declarative, conclusion-oriented title; one chart or table; 2-4 supporting bullets; and a source footer. See the style guide for details.
8. **Visual-text integration**: Every chart or table must serve a clear analytical purpose. Titles should state the finding, not merely describe the data. Body text interprets the visual's implications rather than repeating its numbers.
9. **Sensitivity analysis**: When a key variable is critical to the thesis (raw material cost, exchange rate, pricing, etc.), include a sensitivity matrix showing how changes in that variable affect the conclusion. See the style guide for the sensitivity table format.

**If using parallel subagents**:
- Each subagent writes its chapter(s) to a temporary file.
- After all subagents complete, merge chapters into the final report in outline order.
- Ensure consistent formatting, terminology, and cross-references across chapters.

### Phase 5: Executive Summary

**Goal**: Produce a concise executive summary that organizes the report's most impactful findings into themes.

**Process**:
1. Re-read the completed report.
2. Identify the 3-6 most important findings. These may or may not align one-to-one with chapters — group related findings from different chapters into thematic clusters if they form a coherent investment narrative.
3. organize the summary by theme, with each theme supported by 2-4 data-backed bullet points.
4. Insert the executive summary immediately after the report title and before Chapter 1.

**Executive summary format**:

```markdown
## Executive Summary

### [Theme 1 — a complete conclusion statement]
- [Key finding with number] — [1-sentence implication]
- [Key finding with number] — [1-sentence implication]
- [Key finding with number] — [1-sentence implication]

### [Theme 2 — a complete conclusion statement]
- [Key finding with number] — [1-sentence implication]
- [Key finding with number] — [1-sentence implication]

...
```

**Theme-first vs chapter-first**: Prefer theme-based grouping when the report covers interconnected topics (e.g., supply chain, demand, cost, and competition are all linked). Reserve chapter-by-chapter summaries for reports where chapters are isolated standalone analyses.

### Phase 6: Data Verification Checklist

**Goal**: Systematically verify all data citations in the report for accuracy and completeness.

**Process**:

1. **Extract all data claims**: Scan the report for every quantitative claim (numbers, percentages, dates, rankings, quotes).
2. **Verify against source**: For each claim, trace it back to the cited source and confirm accuracy.
3. **Check categories**:
   - ✅ **Verified**: Data matches source exactly
   - ⚠️ **Minor discrepancy**: Small rounding or formatting difference
   - ❌ **Error**: Data does not match source, or source not found
   - 🔍 **Unverifiable**: Web-sourced data that cannot be re-checked in local materials

4. **Generate checklist report**: Produce a verification checklist as a separate file named:
   - Chinese: `数据验证清单.md`
   - English: `Data_Verification_Checklist.md`

**Checklist format**:

```markdown
# Data Verification Checklist

Report: [Report filename]
Verification date: [Date]
Total data points checked: [N]

## Summary
- ✅ Verified: X / N (XX%)
- ⚠️ Minor discrepancy: X / N (XX%)
- ❌ Error: X / N (XX%)
- 🔍 Unverifiable: X / N (XX%)

## Detailed Checklist

### Chapter 1: [Title]

| # | Data Claim | Source Cited | Verification | Notes |
|---|-----------|-------------|--------------|-------|
| 1 | "Revenue was $240.56B" | BOM Estimate, Sheet "Summary", Row 5 | ✅ Verified | Exact match |
| 2 | ... | ... | ... | ... |

### Chapter 2: [Title]
...
```

5. **Fix errors**: If any ❌ errors are found, correct them in the report and note the correction in the checklist.

## Deliverables

At the end of the workflow, deliver the following files to the user:

1. **Research Report** (`关于XX的研究报告.md` or `XX_Research_Report.md`) — the complete report with executive summary
2. **Data Verification Checklist** (`数据验证清单.md` or `Data_Verification_Checklist.md`) — the verification audit

Use `deliver_attachments` to send both files to the user.

## Error Handling

- **File read failure**: If a file cannot be read (corrupted, password-protected, unsupported format), log it in the material overview and inform the user. Do not silently skip files.
- **Insufficient materials**: If the provided materials cannot adequately answer a chapter's questions, clearly state what is missing and offer to supplement with web research.
- **Conflicting data**: When sources contradict each other, present both figures with their sources and note the discrepancy. Let the analysis explain which is more reliable and why.
- **Large context**: If the total material exceeds context limits, prioritize data directly relevant to the user's questions. Use targeted reads (specific sheets, specific sections) rather than loading entire files.
