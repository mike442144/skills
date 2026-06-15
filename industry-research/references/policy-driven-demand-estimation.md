# Policy-Driven Commodity Demand Estimation

When government policy documents announce quantitative targets (e.g., "改造老旧小区11.5万个", "地下管网36.5万公里"), use this bottom-up framework to estimate incremental demand for upstream commodities (cement, steel, copper, glass, etc.).

## 5-Step Methodology

### Step 1: Extract Quantitative Targets
From policy documents, identify all explicit numerical targets with units:
- Housing: 套数/间数 (e.g., 危旧房50万套, 老旧小区11.5万个)
- Infrastructure: 公里数 (e.g., 地下管网36.5万公里)
- Public space: 公顷数/个数 (e.g., 公园绿地2万公顷, 老旧街区1500个)

### Step 2: Convert Units to Physical Area/Volume
Use historical data or industry benchmarks to convert policy units to physical measures:
- **老旧小区**: Derive average households per community from past data (e.g., "十四五" 24万个惠及4000万户 → 167户/个). Multiply by average unit area (60-90㎡ for old communities) → total ㎡.
- **城中村**: Use brokerage research estimates for total floor area per village (varies by city tier; 超大特大城市 ~20-30万㎡/个, 二三线城市 ~10-15万㎡/个).
- **危旧房**: Average 60-70㎡/套.
- **地下管网**: Already in km; no conversion needed.

### Step 3: Apply Commodity Intensity Factors
Estimate per-unit commodity consumption based on project type:

| Project Type | Cement Intensity | Notes |
|---|---|---|
| New construction (住宅) | ~200-250 kg/㎡ | Full structural + finishing |
| Old community renovation (综合整治) | ~50-80 kg/㎡ | Facade, roads, elevators, pipes; ~1/3 of new build |
| Urban village rebuild (拆除重建) | ~200-250 kg/㎡ | New construction intensity |
| Urban village upgrade (整治提升) | ~20-40 kg/㎡ | Light renovation only |
| Underground pipe network | ~400-600 吨/km | Trench, pipe base, concrete pipes, road restoration |
| Comprehensive utility tunnel (综合管廊) | ~10,000-20,000 吨/km | Much higher due to reinforced concrete structure |

**Key principle**: Distinguish between "renovation" (改造/整治) and "reconstruction" (拆除重建/原拆原建). Renovation uses 20-40% of the commodity intensity of new construction.

### Step 4: Calculate Total Demand
Sum across all project types:
```
Total Demand = Σ (Physical Area/Length × Commodity Intensity Factor)
```
Express as 5-year cumulative total and annual average.

### Step 5: Assess Support Strength vs. Total Industry Demand
Compare policy-driven demand against current total industry output:
- **Support ratio** = Policy-driven annual demand / Current annual industry output
- **Interpretation**:
  - < 3%: Negligible support
  - 3-7%: Meaningful floor support (止血绷带) — slows decline but doesn't reverse it
  - 7-15%: Strong bottom (强支撑) — can stabilize demand at current levels
  - > 15%: Growth driver (增长引擎) — can drive industry recovery

## Example: Cement Demand from 15th FYP Urban Renewal

| Category | Policy Target | Physical Measure | Cement Intensity | 5-Year Demand |
|---|---|---|---|---|
| 老旧小区改造 | 11.5万个 | ~14.4亿㎡ | 70 kg/㎡ | 1.0-1.2亿吨 |
| 地下管网改造 | 36.5万公里 | 36.5万km | 500 吨/km | 1.8-2.0亿吨 |
| 城中村改造 | 4000个 | ~8-10亿㎡ (30% rebuild) | 250 kg/㎡ (rebuild) + 30 kg/㎡ (upgrade) | 0.9-1.0亿吨 |
| 危旧房改造 | 50万套 | ~0.33亿㎡ | 250 kg/㎡ | 0.08亿吨 |
| 其他公共空间 | 1500个街区+公园 | — | — | 0.3-0.5亿吨 |
| **Total** | — | — | — | **4.0-4.7亿吨 (5年)** |
| **Annual avg** | — | — | — | **0.8-0.9亿吨/年** |

vs. 2025 national cement output ~16.9亿吨 → support ratio ~5%.
Conclusion: meaningful floor support but cannot reverse structural decline from real estate downturn.

## Data Sources for Benchmarks

- **Historical policy execution data**: Government work reports, ministry press conferences (住建部发布会)
- **Industry intensity factors**: Brokerage research reports (东方财富, 同花顺), industry associations (中国水泥协会), academic papers
- **Total industry output**: National Bureau of Statistics (国家统计局), Wind EDB database, industry association annual reports
- **Unit conversion ratios**: Derive from past policy periods (e.g., "十四五" execution data for 老旧小区 → 户均面积)

## Pitfalls

1. **Don't double-count**: Some projects span multiple categories (e.g., 城中村改造 may include 管网改造). Be conservative in summation.
2. **Renovation vs. reconstruction intensity**: Policy documents often use "改造" ambiguously. Verify whether it means light renovation (整治) or demolition + rebuild (拆除重建). Intensity differs by 5-8x.
3. **Time phasing**: Policy targets are usually 5-year cumulative. Annual rollout may be front-loaded or back-loaded depending on funding availability.
4. **Regional heterogeneity**: Unit costs and commodity intensity vary significantly by region (tier-1 cities vs. tier-3/4). Use national averages for rough estimates, but flag regional variation for detailed analysis.
5. **Substitution effects**: Some commodities may be substituted (e.g., PVC pipes vs. concrete pipes, steel structure vs. reinforced concrete). Verify dominant technology for the specific application.
