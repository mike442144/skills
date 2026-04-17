# Data Verification Guide

This reference provides the detailed methodology for Phase 6 (Data Verification Checklist) of the research report workflow.

## Verification Process

### Step 1: Extract Data Claims

Scan the completed report and extract every instance of:

- **Absolute numbers**: Revenue figures, unit counts, headcounts, prices
- **Percentages**: Growth rates, margins, market shares, ratios
- **Rankings**: "#1 in market", "second largest", "top 3"
- **Time-bound claims**: "since 2025", "in Q4", "year-over-year"
- **Quotes**: Direct quotes from expert interviews or documents
- **Comparisons**: "higher than X", "outperformed Y", "declined from A to B"

### Step 2: Trace to Source

For each extracted claim:

1. Identify the cited source (file, sheet, section)
2. Navigate to the exact location in the source material
3. Extract the original value
4. Compare with the report's stated value

### Step 3: Classification

Apply the following classification:

| Status | Symbol | Criteria |
|--------|--------|----------|
| Verified | ✅ | Exact match between report claim and source data |
| Minor discrepancy | ⚠️ | Difference due to rounding, unit conversion, or minor formatting (e.g., $240.56B vs $240.6B) |
| Error | ❌ | Material difference between report claim and source data, or source cannot be located in cited file |
| Unverifiable | 🔍 | Data sourced from web search or external reference that cannot be re-verified against local materials |

### Step 4: Error Resolution

For each ❌ Error found:

1. Identify the correct value from the source
2. Update the report with the corrected value
3. Note the correction in the checklist with both original and corrected values

### Step 5: Summary Statistics

Calculate and report:
- Total data points checked
- Count and percentage for each status category
- Per-chapter breakdown

## Checklist Template

```markdown
# Data Verification Checklist

**Report**: [Report filename]
**Verification date**: [YYYY-MM-DD]
**Total data points checked**: [N]

## Summary

| Status | Count | Percentage |
|--------|-------|------------|
| ✅ Verified | X | XX% |
| ⚠️ Minor discrepancy | X | XX% |
| ❌ Error | X | XX% |
| 🔍 Unverifiable | X | XX% |

## Corrections Made

| # | Chapter | Original Value | Corrected Value | Source |
|---|---------|---------------|-----------------|--------|
| 1 | Ch.2    | DRAM price $55 | DRAM price $60  | BOM Estimate, Sheet "Memory Price" |

## Detailed Checklist

### Chapter 1: [Title]

| # | Data Claim | Source Cited | Status | Notes |
|---|-----------|-------------|--------|-------|
| 1 | "Global sell-in reached 17.36M units" | HW Sales.xlsx, Sheet "Forecast", Row 12 | ✅ | Exact match |
| 2 | "Holiday quarter peaked at 7.00M" | HW Sales.xlsx, Sheet "Forecast", Row 8 | ✅ | Exact match |
| 3 | ... | ... | ... | ... |

### Chapter 2: [Title]
...
```

## Priority Rules

When verification time is limited, prioritize checking:

1. **Executive Summary claims** — these are the most visible and impactful
2. **Numerical claims in bold text** — the author highlighted these as important
3. **Claims that drive conclusions** — if the conclusion changes when the number changes, verify it
4. **Cross-referenced data** — data cited from multiple sources should be consistent
5. **Web-sourced data** — mark as 🔍 and note the original URL/source for user reference
