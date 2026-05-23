# M&A Data Collection Pattern for A-Share Research

When researching A-share companies with recent acquisitions (within 1-2 years), follow this pattern:

## Problem

Consolidated financial reports blend pre- and post-acquisition data, making:
- YoY revenue/profit growth misleading (acquired entity's full-year revenue appears in the acquirer's consolidated statement only from acquisition date)
- Segment-level analysis impossible without separate queries
- Goodwill and integration risks invisible at the consolidated level

## Solution

### 1. Query each entity separately

For each acquired entity, run separate data queries:
```
# Example: 华润三九 (000999) + 天士力 (600535) + 昆药集团 (600422)
mx-finance-data: "华润三九000999最新财报数据"
mx-finance-data: "天士力600535最新财报数据"  
mx-finance-data: "昆药集团600422最新财报数据"
mx-finance-data: "天士力600535主营构成分析"
mx-finance-data: "昆药集团600422主营构成分析"
```

### 2. Map the acquisition timeline

Key dates to identify:
- **Announcement date**: When the deal was first disclosed
- **Closing date / 并表日**: When the acquired entity's financials start being consolidated
- **First full-year consolidation**: When the acquired entity's full fiscal year appears in consolidated statements

Example: 天士力 was acquired in March 2025, so 2025 annual report includes ~10 months of 天士力 revenue, making YoY growth artificially inflated.

### 3. Segment attribution in the report

In Section 2 (Business Deep Dive):
- Parent company: standalone financials, organic growth rates
- Each acquired entity: separate subsection with its own revenue, profit, margin, and growth
- Note the consolidation status explicitly (e.g., "天士力自2025年3月并表，贡献营收约XX亿元")
- Flag any data that is estimated/interpolated by analysts vs. company-disclosed

### 4. Risk flags to include

- **Goodwill impairment**: If acquisition price significantly exceeded book value
- **Integration risk**: Cultural fit, system integration, channel overlap
- **同业竞争**: Regulatory requirement to resolve overlapping business lines within a set timeframe (usually 3-5 years for A-share)
- **业绩承诺 (earn-out)**: If the acquired party has performance commitments, note the targets and current status

### 5. Common A-share M&A patterns

| Pattern | Example | Risk |
|---------|---------|------|
| 央企整合 (SOE consolidation) | 华润三九收购天士力、昆药 | Policy-driven, usually smooth integration but slow |
| 产业链延伸 (vertical integration) | Pharma company acquiring distributor | Channel conflict, margin compression |
| 跨界并购 (cross-industry M&A) | Traditional company buying tech startup | High goodwill, integration failure risk |
| 借壳上市 (backdoor listing) | Private company listing via shell company | Asset quality concerns, regulatory scrutiny |
