# Multi-Company Parallel Research Pattern

When researching an entire industry by generating deep-dive reports for multiple listed companies in parallel, then synthesizing an industry report.

## Workflow

```
Phase 1: Parallel subagent × N — each generates one company deep-dive report
         ↓
Phase 2: Cross-reference pass — fix competitor comparison tables across all reports
         ↓
Phase 3: Main agent — industry-level report using company reports as data sources
```

## Phase 1: Parallel Subagents

Each subagent independently researches one company using `listed-company-research` skill.

**Delegation pattern:** Use `delegate_task` with `tasks` array (up to 3 concurrent) for parallel execution. If more than 3 companies, split into batches.

**Critical context to pass to each subagent:**
- Working directory, wind-mcp-skill path, Wind MCP quoting rules
- google-workspace skill path (for CIQ Google Sheets access)
- List of ALL other companies being researched (so they know who to compare against)
- Any already-existing report (e.g., 三鑫医疗 already done) for reference

## Phase 2: Cross-Reference Pass (MANDATORY)

**Problem:** Each subagent's competitor comparison tables contain stale/estimated data for other companies because they were written before other reports finished. Common issues:
- Revenue figures from prior year (e.g., using 2024 data when 2025 annual is available)
- Market cap estimates like "~30-40亿" instead of exact values
- Missing competitors entirely
- Inconsistent market share data across reports

**Fix process:**
1. Read all N company reports
2. Extract latest financial data from each (2025 annual + 2026Q1)
3. Update each report's competitor comparison table with consistent, current data
4. Ensure market share data is consistent across all reports (use most authoritative source — usually 招股书/弗若斯特沙利文 or 中国血液净化期刊)

**Data to synchronize across all reports:**
- 2025 annual revenue, net profit, gross margin for each company
- Current market cap, PE-TTM, ROE
- Market share figures (透析器/管路/设备/灌流器)

## Phase 3: Industry Report (Main Agent)

Use `industry-research` skill. Company reports serve as primary data sources.

**Data to aggregate from company reports:**
- Combined market coverage (which segments each company plays in)
- Revenue/profit comparison table
- Technology/innovation pipeline across all players
- Policy impact assessment (how each company responds to 集采)

**Supplement with external sources:**
- Industry association reports (中国血液净化期刊, 智研咨询, 头豹研究院)
- Policy documents (国家医保局, 卫健委)
- Market research (弗若斯特沙利文 from 招股书)

## Pitfalls

1. **Subagent data freshness**: Subagents may not check for the absolute latest data. Always verify shareholder data uses latest quarter (not annual report) and segment revenue uses latest annual report.
2. **Wind MCP limits**: 4+ parallel subagents querying Wind MCP simultaneously may trigger rate limiting. Consider staggering or accepting that some data may need fallback.
3. **Competitor matrix inconsistency**: Without Phase 2, the industry report will inherit inconsistent data from company reports. Always do the cross-reference pass.
4. **Different report formats**: Each subagent may use slightly different formatting for tables/metrics. The industry report should normalize these.
