# Listed Company Research Report Template

This template defines the full structure and writing guidance for the research report.

**Adaptation principle:** This template is a structural guide, not a straitjacket. For each section, ask: "Does this sub-section meaningfully apply to THIS company?" If a sub-section is irrelevant (e.g., "Production Inputs" for a pure platform company, "M&A track record" for a company that has never acquired), simplify it to one sentence or omit it. Depth should follow relevance — go deep where the company's story demands it, stay brief where it doesn. A focused 4,000-word report beats a padded 8,000-word one.

---

## Report Metadata

At the very top of the report, include:

```markdown
# [Stock Short Name] ([Stock Code]) In-Depth Research Report

> **Research Date:** YYYY-MM-DD
> **Data As Of:** [most recent fiscal year/quarter covered by collected data]
> **Disclaimer:** This report is for information and educational purposes only. It does not constitute any investment advice, rating, or recommendation. Information herein is sourced from public channels; the author does not guarantee its completeness or accuracy.
```

---

## 1. Company Overview

### 1.1 Basic Information

Present in a clean table format:

| Field | Content |
|-------|---------|
| Full Registered Name | [Full name in local language and English if applicable] |
| Exchange | [SSE / SZSE / HKEX / NYSE / NASDAQ] |
| Stock Code | [Code with market prefix, e.g., 600519.SH, 02858.HK, AAPL] |
| Industry | [Primary classification; note which system: GICS / local regulator classification] |
| Headquarters | [City, Province/Country] |
| Primary Office | [If different from HQ] |
| Website | [URL] |

### 1.2 Ownership Structure

**按上市市场区分股东信息披露方式：**

#### A股（沪深北）

Present the top 10 shareholders (前十大股东) in a detailed table:

| Rank | Shareholder | Stake (%) | Shares Held | Share Type | Restrictions | Shareholder Type |
|------|-------------|-----------|-------------|------------|--------------|------------------|
| 1 | [Controlling Shareholder] | [%] | [shares] | [A/B/优先股] | [限售/流通] | [分类见下方] |
| 2 | [2nd largest] | [%] | [shares] | [A/B/优先股] | [限售/流通] | [分类见下方] |
| ... | ... | ... | ... | ... | ... | ... |
| 10 | [10th largest] | [%] | [shares] | [A/B/优先股] | [限售/流通] | [分类见下方] |

Shareholder Type 分类：控股股东/实控人、国有股东(国资委/地方国资)、国家队(汇金/证金/中央汇金资管等)、社保基金、公募基金、私募基金、外资(QFII/陆股通)、险资、牛散、其他法人、其他自然人。

[Brief narrative: ownership concentration, actual controller (实际控制人), top 10 combined stake %, free float %. Note presence of special shareholder types (国家队、社保基金、险资、知名牛散等) and implications for corporate governance. Flag any significant recent changes in ownership structure.]

#### 港股（HKEX）

获取 **5%以上主要股东** 列表（来自年报"Substantial Shareholders"章节或港交所权益披露公告）：

| Shareholder | Stake (%) | Shares Held | Long/Short Position | Capacity | Notes |
|-------------|-----------|-------------|---------------------|----------|-------|
| [控股股东] | [%] | [shares] | Long | [实益拥有人/受控法团] | [...] |
| [...] | ... | ... | ... | ... | ... |

**注意事项：**
- 控股股东通过多层架构持股的，需在控股结构图中注明（如：集团 → 子公司A → 上市公司）
- 如能找到 CCASS 持仓集中度数据（前十大经纪商名义持股占比），可简要补充说明散户/机构集中度情况
- [Brief narrative: 实际控制人、控股股东合计持股比例、公众持股量是否满足港交所最低25%要求、近年是否有重要股东变动]

#### 美股（NYSE/NASDAQ）

获取 **机构持股（Institutional Ownership）** 和 **内部人持股（Insider Ownership）** 信息（来自 SEC 13F/13G  filings 及 proxy statement DEF 14A）：

| Holder Type | Top Holder | Stake (%) | Shares Held | Filing Type |
|-------------|-----------|-----------|-------------|-------------|
| Institution 1 | [...] | [%] | [shares] | 13F |
| Institution 2 | [...] | [%] | [shares] | 13G |
| Insider 1 | [...] | [%] | [shares] | Form 4 |
| ... | ... | ... | ... | ... |

**注意事项：**
- 13F 文件仅披露超过1亿美元的机构季度持仓，不代表全部持股
- 13G 文件披露持股超过5%的被动投资者
- [Brief narrative: 机构持股比例、内部人持股比例、是否有 activist investor、控制权结构（A/B股投票权差异如有）]

### 1.3 Company Profile

Write 2-4 paragraphs covering:
- What the company does in one clear sentence (the "elevator pitch")
- When it was founded, key milestones in its history
- Its current scale (revenue range, market position)
- Any notable transformations, pivots, or strategic shifts in its history

**Writing guidance:** Keep it factual. Use specific dates and numbers. Avoid marketing language. If the company has gone through significant business model changes, describe the evolution clearly.

---

## 2. Business Deep Dive

This is the core section. Start with a multi-year revenue composition overview, then dive into each segment.

### 2.0 Revenue Composition (Last 3 Years)

Present a multi-year segment revenue breakdown table before diving into each segment's details. Source data from the latest annual report (if more recent quarterly disclosures contain updated segment data, use those and note the source).

| Segment | [Year-2] Revenue | [Year-2] % | [Year-1] Revenue | [Year-1] % | [Year] Revenue | [Year] % | YoY |
|---------|:-----------:|:-----:|:-----------:|:-----:|:-----------:|:-----:|:---:|
| [Segment 1] | XX亿 | XX% | XX亿 | XX% | XX亿 | XX% | +XX% |
| [Segment 2] | XX亿 | XX% | XX亿 | XX% | XX亿 | XX% | -XX% |
| [Segment 3] | XX亿 | XX% | XX亿 | XX% | XX亿 | XX% | XX% |
| Others / Inter-segment elimination | XX亿 | XX% | XX亿 | XX% | XX亿 | XX% | — |
| **Total** | **XX亿** | **100%** | **XX亿** | **100%** | **XX亿** | **100%** | **+XX%** |

> **Data source accuracy:** The table above must reflect the company's OWN reported segment definitions and reported figures (from the annual report's "分部报告" / "segment reporting" section). Do NOT reclassify or aggregate segments unless the company does so. If segment definitions changed during the 3-year window (e.g., a segment was renamed, split, or merged), explicitly note the reclassification and align the historical data using restated comparatives; if restated figures are not available, flag the discontinuity.

**Segment Profitability (when disclosed):**

If the annual report discloses segment-level profit or gross margin data (分部利润 / 分产品毛利率), present it alongside revenue:

| Segment | [Year-2] Gross Margin | [Year-1] Gross Margin | [Year] Gross Margin | Margin Δ (YoY) |
|---------|:-----------:|:-----------:|:-----------:|:---:|
| [Segment 1] | XX% | XX% | XX% | +X.Xpp |
| [Segment 2] | XX% | XX% | XX% | -X.Xpp |
| [Segment 3] | XX% | XX% | XX% | X.Xpp |
| **Overall** | **XX%** | **XX%** | **XX%** | **+X.Xpp** |

Use gross margin (毛利率) as the default metric. If the company discloses operating profit by segment instead (or additionally), use that and label accordingly. If segment profitability is NOT disclosed, skip this table and note the gap in one sentence.

**Key observations (2-3 concise points):**
- **Growth engine vs. cash cow:** Which segments are growing above/below the company average? Is there a clear "second curve" emerging?
- **Concentration:** Does any single segment contribute >50% of total revenue? Is revenue diversified or highly concentrated?
- **Profitability skew (if disclosed):** When segment profitability is available, does the revenue share align with profit contribution, or does one segment punch above/below its weight?

### 2.X [Segment Name]

For each segment, answer these core questions in narrative form. Not every question requires equal depth — weight them by relevance to this specific business.

#### What Is This Business?

Explain in plain language what this segment does, who it serves, and what problem it solves. Name the business model if applicable (platform, subscription, usage-based, manufacturing, etc.).

#### Customers & Revenue Model

Who pays, for what, and how? Cover: customer profile (B2B/B2C/B2G), concentration (few large clients vs. distributed), revenue mechanism (fees, spreads, subscriptions, product sales), and key revenue drivers.

#### Key Inputs & Resources

What does this business need to operate? **Adapt depth to asset intensity:**
- Heavy-asset / manufacturing: detail physical inputs (equipment, facilities, raw materials), capacity constraints, supply chain dependencies
- Asset-light / platform / service: one paragraph on key resources (talent, technology, licenses, data) is sufficient — do NOT force-fit a manufacturing framework

#### Business Process (when non-obvious)

Walk through the value creation chain only if the process is complex or non-intuitive. For straightforward businesses (e.g., "makes product → sells product"), skip this sub-section. When included, a simple text flow chart is effective:

```
Customer Acquisition -> [Step 1] -> [Step 2] -> [Step 3] -> Revenue Recognition -> Post-Sale
```

#### Key Operating Metrics

List 3-5 metrics that best reflect this segment's health (e.g., GMV, active users, ARPU, utilization rate, renewal rate, default rate). Provide latest figures with sources. Choose metrics that a reader would track to judge whether this business is thriving — not a generic KPI dump.

#### Revenue Contribution

Revenue amount, % of total, growth trend (YoY + multi-year), and segment profitability if disclosed.

### Inter-Segment Synergies

After describing all segments, add a subsection analyzing:
- How the different business segments relate to and support each other
- Whether there are cross-selling opportunities or shared resources

**Important writing principles for this section:**
- Prioritize clarity over comprehensiveness — it is better to explain 3 things well than 7 things superficially
- Always explain what an industry term means the first time it is used
- Use concrete examples wherever possible
- If certain information is not publicly available, explicitly state "This information was not disclosed in publicly available sources."

---

## 3. Industry & Competitive Landscape

### 3.1 Industry Overview

- Define the industry scope and boundaries
- Total addressable market (TAM) size with source and year
- Market growth rate (historical and projected)
- Key growth drivers and structural trends
- Major policy or regulatory environment affecting the industry

### 3.2 Value Chain Analysis

**Adapt to business type:** For manufacturing, commodity, or supply-chain-intensive businesses, map the full value chain (upstream → midstream → downstream) with key players, bargaining power dynamics, and bottlenecks. For platform, service, or asset-light businesses, reframe as an "ecosystem map" (who are the participants, how does value flow between them) or simplify to 1-2 paragraphs if the value chain is straightforward and uninformative.

```
Upstream (Suppliers) -> Midstream (This Industry) -> Downstream (Customers / End Users)
```

For each relevant layer: who are the key players, what is the bargaining power dynamic, and are there bottlenecks or dependencies?

### 3.3 Competitive Landscape

Present the competitive landscape:

**Market concentration:**
- Is the market fragmented or concentrated?
- CR3/CR5/HHI if available
- Market share of top competitors

**Competitor matrix (include 5-10 major competitors):**

| Company | Core Business | Market Position | Scale / Revenue | Key Strengths | Key Weaknesses / Differentiation |
|---------|---------------|-----------------|-----------------|---------------|----------------------------------|
| [Target company] | ... | ... | ... | ... | ... |
| [Competitor 1] | ... | ... | ... | ... | ... |
| [Competitor 2] | ... | ... | ... | ... | ... |
| ... | | | | | |

**Competitive dynamics:**
- What are the main dimensions of competition? (price, technology, brand, distribution, regulation, network effects, etc.)
- Are there significant barriers to entry? What are they?
- Is the industry seeing consolidation, new entrants, or disruption?
- How does the target company differentiate from competitors?

### 3.4 Company Positioning in the Industry

- Where does the target company sit in the competitive landscape?
- What is its defensible moat (if any)? Be specific

**Moat Assessment:**

Evaluate each moat dimension on a 4-level scale and provide specific evidence for the rating.

| Moat Dimension | Strength | Evidence / Rationale |
|---------------|----------|----------------------|
| Scale advantages / cost leadership | 🟢 Strong / 🟡 Moderate / 🔴 Weak / ➖ None | [Brief evidence: cost position relative to peers, market share, production efficiency, etc.] |
| Network effects / platform economics | 🟢 Strong / 🟡 Moderate / 🔴 Weak / ➖ None | [Evidence of network effects: user growth feeding value, multi-sided platform dynamics] |
| Switching costs / customer stickiness | 🟢 Strong / 🟡 Moderate / 🔴 Weak / ➖ None | [Evidence: contract structures, integration depth, churn rate, replacement difficulty] |
| Regulatory licenses / compliance | 🟢 Strong / 🟡 Moderate / 🔴 Weak / ➖ None | [Evidence: license exclusivity, regulatory barriers to entry, compliance moat] |
| Brand / reputation | 🟢 Strong / 🟡 Moderate / 🔴 Weak / ➖ None | [Evidence: brand recognition, pricing power from brand, NPS/customer loyalty metrics] |
| Technology / data advantages | 🟢 Strong / 🟡 Moderate / 🔴 Weak / ➖ None | [Evidence: patent portfolio, R&D intensity, proprietary data assets, tech lead time] |
| Distribution channel advantages | 🟢 Strong / 🟡 Moderate / 🔴 Weak / ➖ None | [Evidence: channel exclusivity, geographic coverage, distributor relationships] |

**Overall Moat Rating:**

Rate on a 5-level scale. The core logic mirrors Morningstar's duration framework: the key question is not "does the company have advantages now?" but "how long can it sustain economic profits (ROIC > WACC) before competition erodes them?" Use the expected duration as the primary anchor, then calibrate based on moat source quality and threat intensity.

In the delivered report, present the rating in this format (delete the other 4 options):

**Overall Moat Rating:[Rating Emoji] [Rating Level] — Expected Duration: [duration range]**
[2–3 sentence rationale: why this rating? What are the specific irreplicable dimensions, structural barriers, or limits? Be concrete and specific to this company — not generic template language.]

Rating scale:

| Rating | Emoji | Expected Duration | Key Characteristics |
|--------|-------|-------------------|---------------------|
| Exceptional | 🟢🟢 | > 30 years | Multiple strong dimensions that mutually reinforce. Moat is deeply entrenched — competitors face multi-layered structural barriers. |
| Wide | 🟢 | 20–30 years | At least one dimension is Strong, others at least Moderate. Advantages rooted in structural factors that cannot be quickly replicated or bought. |
| Narrow | 🟡 | 10–20 years | Advantages exist but measurable in their limits. Moat buys time rather than permanent protection. May have only Moderate ratings across multiple dimensions. |
| Emerging / Eroding | 🟠 | Uncertain / < 10 yr | Competitive position in flux — new moat forming or existing one being breached (tech disruption, regulatory change, competitor incursion). |
| None | 🔴 | < 10 years or never | No durable structural advantage. Current profitability explainable by cyclical factors, temporary product leadership, or favorable conditions. |

**Moat Trajectory:**

In the delivered report, present the trajectory in this format:

**Moat Trajectory: ⬆️ Strengthening / ➡️ Stable / ⬇️ Weakening**
[1–2 sentence rationale: what is driving near-term moat widening or narrowing? Distinguish cyclical headwinds from structural erosion. If Stable, note any early-warning signals worth monitoring.]

**Emerging Threats:**

- Are there identifiable threats that could shrink the moat within the expected duration window?
- Distinguish between: (a) distant theoretical risks, (b) early-stage technological/regulatory shifts, and (c) active competitive incursions already materializing.

---

## 4. Management Outlook & Strategy

This section captures management's own voice on where the company stands and where it is headed. It serves as a bridge between external industry analysis (Chapter 3) and risk assessment (Chapter 5), enabling a "challenges stated externally → responses articulated internally" cross-check.

### 4.1 Management View on Current Situation

Extract and analyze how management perceives the current landscape.

**Core elements to cover:**

- **Management's assessment of industry/market conditions:** What does leadership believe is happening in their market right now? (Quote key passages from MD&A or earnings calls, then interpret)
- **Attribution analysis for recent performance:** How does management explain recent results — good or bad? Do they attribute strengths to strategy/skill and weaknesses to external factors? Watch for self-serving bias
- **Key opportunities and challenges as seen by management:** What keeps them up at night? What are they most excited about? List specific items they highlight
- **Cross-check with Chapter 3:** Compare management's view against your independent industry analysis in Section 3. Note areas of alignment and divergence. Is management being overly optimistic, appropriately cautious, or surprisingly candid?

**Information sources (in priority order):**
1. Annual Report → "Management Discussion and Analysis" (MD&A) chapter — usually the richest single source
2. Earnings call transcript (latest quarter) — Q&A session often reveals unscripted views
3. Investor presentation / deck (latest version) — strategic narrative in management's own framing
4. Chairman/CEO public interviews, speeches, or shareholder letters

**Writing principles:**
- Use direct quotes where powerful; always provide translation/interpretation for non-English quotes
- Maintain a critical but fair tone — you are reporting what management says, not endorsing it
- Flag any notable omissions: what is management NOT talking about that they should be?

### 4.2 Strategic Plans & Future Direction

Document where management intends to take the company.

**Core elements to cover:**

- **Medium-to-long-term strategic direction (3–5 year horizon):** What is the stated vision? Any specific targets (revenue, market share, profitability metrics, scale goals)?
- **Specific strategic initiatives:**
  - New business lines or product launches (what, timeline, expected contribution)
  - Market expansion plans (geographic new entry, customer segment penetration)
  - Technology / R&D investment priorities (AI, digital transformation, platform upgrades)
  - Organizational changes (restructuring, M&A pipeline, partnership/JV strategies)
- **Capital allocation plan:**
  - CAPEX guidance: how much, on what, over what period
  - M&A appetite: any announced targets, deal size parameters, focus areas
  - Shareholder return policy: dividend policy (payout ratio trend, sustainability), share buyback program (authorization, utilization)
  - Balance sheet target leverage or credit rating aspirations
- **Key milestones and timetable:** Any publicly disclosed roadmap with dates or phases

**Information sources:**
1. Strategic plan section in annual report MD&A
2. Investor day materials / capital markets day presentation
3. Earnings call guidance section (forward-looking statements)
4. Company press releases on major strategic initiatives
5. Proxy statement (for compensation-aligned strategic goals)

**Writing principles:**
- Distinguish between firm commitments ("we will"), near-term guidance ("we expect"), and aspirational language ("we aim to", "we explore")
- Present quantified targets in a table format for clarity:

| Strategic Target | Metric | Stated Goal | Timeline | Current Progress |
|------------------|--------|-------------|----------|-----------------|
| [e.g., Revenue] | CNY bn | [X] by [Year] | [Year] | [Y] (as of latest) |
| ... | | | | |

### 4.3 Track Record: Words vs. Actions

Assess management's credibility by comparing past statements against actual outcomes. This subsection is critical for judging whether future strategic plans (Section 4.2) are likely to materialize.

**Methodology:**

If the multi-year MD&A review was performed (see `references/multi-year-mda-review.md`), this section should be built directly from its output — specifically dimensions 3.1 (Strategic Timeline) and 3.4 (Guidance vs Actuals). Do not re-collect past statements ad hoc; the structured MD&A review already covers the full period systematically.

If the MD&A review was NOT performed (e.g., recent IPO with <5 years of history, or abbreviated research), fall back to manual collection:
1. **Collect past statements:** Go back 2–3 years and find management's then-stated strategic goals, guidance, or commitments from:
   - Prior annual reports (MD&A sections from 2–3 years ago)
   - Prior investor presentations
   - Prior earnings call transcripts (guidance given)
2. **Compare against actuals:** For each stated goal, determine:
   - **Achieved:** Did they do what they said? By when?
   - **Partially achieved:** Close but not quite — why?
   - **Not achieved / Missed:** Did they abandon it quietly? Address the miss openly?
   - **Ongoing / Too early to judge:** Still in progress
3. **Identify patterns:**
   - Does management tend to **over-promise and under-deliver** (optimistic bias)?
   - Are they **conservative** (under-promise, over-deliver)?
   - Do they **pivot frequently** (strategic inconsistency) or **stay the course** (strategic discipline)?
   - How do they handle misses — transparently or with excuses?

**Output format — Credibility Scorecard:**

| Past Statement (Year) | Stated Goal | Status | Actual Outcome | Management Response to Miss |
|-----------------------|-------------|--------|----------------|----------------------------|
| [Quote + source] | [What they said] | ✅ Achieved / ⚠️ Partial / ❌ Missed | [What happened] | [How they explained it] |

**Overall assessment paragraph (2–4 sentences):** Summarize the pattern and give a qualitative judgment on management's forecasting reliability and strategic execution track record.

### 4.4 Capital Allocation Track Record (资本配置能力回顾)

**Core question:** Regardless of what management said, did they deploy shareholder capital wisely and create (or destroy) value over time?

This is distinct from §4.3 (which tests whether management keeps its word). Here the lens is actual financial outcomes.

**Methodology:**

Collect 5+ years of data: cash flow statements (from productivity-tools CIQ, Wind, or annual reports), CAPEX disclosures, M&A announcements with post-deal outcomes, dividend/buyback history. Build the analysis from actual outcomes, not management narratives.

If the multi-year MD&A review was performed (see `references/multi-year-mda-review.md`), draw on its capital allocation dimensions directly.

**Dimensions (choose the 2-3 most relevant to this company's story):**

- **CAPEX cycle & investment returns** — Investment intensity over time, timing (counter-cyclical vs. pro-cyclical), achieved ROIC vs. WACC for completed projects, white elephant detection. *Most relevant for: capital-intensive manufacturers, utilities, infrastructure.*
- **M&A track record** — Inventory of major deals with post-deal outcomes, goodwill impairment history, serial acquirer vs. disciplined buyer pattern, divestiture timeliness. *Most relevant for: companies with active acquisition history.*
- **Shareholder return consistency** — Dividend policy stability and sustainability (FCF-funded vs. debt-funded), buyback timing (attractive valuations vs. peaks), TSR vs. peer median. *Most relevant for: mature companies where return of capital is a key thesis.*
- **Balance sheet management** — Leverage trajectory, financing timing (equity at highs vs. lows), credit rating trajectory, liquidity management. *Most relevant for: leveraged businesses, financials, companies that went through stress.*
- **Capital discipline signals** — FCF conversion trend, excess cash behavior, growth-at-any-cost detection, related-party capital flows. *Relevant as a cross-cutting check for all companies.*

For dimensions not chosen, a single sentence explaining why they're not material is sufficient (e.g., "The company has never made an acquisition, so M&A track record is not applicable.").

**Output — Overall Capital Allocation Rating:**

**Capital Allocation Rating: [🟢 Value-Creating / 🟡 Neutral / 🔴 Value-Destroying]**

[2–4 sentence narrative: What is the overall pattern? Cite the single most illustrative decision as evidence. Note inflection points (new CFO, strategy shift). Distinguish structural skill from cyclical luck.]

**Writing notes:**
- Be specific: "¥12bn invested in X plant in 2019 at cycle peak; reached only 55% utilization by 2024, impaired ¥2.1bn" — not "some investments underperformed"
- Distinguish skill from luck: one well-timed deal in a rising market ≠ allocation skill
- Note the counterfactual where illuminating

---

## 5. Risk Factors

**Core question:** What could go wrong for THIS specific company, and how severe would it be?

Identify the 5-10 most material risks specific to this company. Organize by category, but only include categories where genuine risks exist — do NOT pad with generic risks that apply to any listed company. A company with no FX exposure doesn't need a "foreign exchange risk" paragraph.

**Categories to consider (include only where material):**

- **Operational** — revenue concentration, business model sustainability, supply chain, key-person dependency, execution risk of stated strategy
- **Financial** — liquidity, leverage, asset quality, earnings volatility
- **Industry** — market saturation/decline, technology disruption, competition intensification, cyclicality
- **Governance & compliance** — related-party transactions, controlling shareholder risk, regulatory exposure
- **Macro & systemic** — geopolitical, sanctions/trade restrictions, macro sensitivity (only for companies with genuine international or macro exposure)

For each risk: state it specifically, quantify the potential impact where possible, assess likelihood, and note any mitigation the company has in place.

### 5.X Risk Summary

Provide a concise summary table of the top risks identified above:

| Risk Category | Key Risk | Impact Level | Likelihood | Mitigation |
|---------------|----------|-------------|------------|------------|
| [Category] | [Specific risk] | 🔴 High / 🟡 Medium / 🟢 Low | 🔴 / 🟡 / 🟢 | [Brief] |

**Writing guidance:**
- Be specific — "Loss of top 3 clients could reduce revenue by >30%" not "There is customer concentration risk"
- Quantify wherever possible
- Avoid generic boilerplate — every risk should be tailored to this company
- If the company's risk profile is genuinely benign, say so briefly rather than inventing risks

---

## 6. Sources & Limitations

### 6.1 Skills Used

List every installed skill that was invoked during this research. If no skills were used, state "No installed skills were used for this research."

| Skill Name | Data Provided | Used For Section |
|------------|---------------|------------------|
| [skill name] | [brief description of data obtained] | [e.g., Section 1.1, Section 3.1] |
| ... | | |

### 6.2 Other Sources

List all other major sources used in the research:

- Company filings: [list specific filings with dates]
- Official sources: [company website, exchange filings, regulatory documents]
- Third-party research: [brokerage reports, industry reports, news sources]
- Data platforms: [e.g., Wind, Bloomberg, public financial databases]

### 6.3 Limitations

Also note:
- Key information gaps (what could not be found)
- Data freshness concerns (how current is the information)
- Any reliance on unaudited or estimated figures

---

## Formatting Guidelines

- Use `##` for main sections (1 through 6)
- Use `###` for sub-sections (e.g., 2.1, 2.2)
- Use tables for structured data wherever possible
- Use bullet points for lists of items
- Use numbered lists for sequential processes
- Bold key terms and important figures for readability
- Include source citations inline: `(Source: 2024 Annual Report)` or `(Source: XYZ Securities Research, 2024)`
- Keep paragraphs concise — aim for 3-5 sentences per paragraph
- Target total report length: 3,000 - 8,000 words depending on company complexity

### Narrative Prose vs Bullet Points — Example

The rule is stated in SKILL.md (conclusion-first narrative for analytical content; bullets only for data/factual lists). Below is a concrete example of the difference:

**Good — narrative:**
> 宇通在这一阶段正式提出了"宇通模式"这个概念。简单来说，传统的中国制造业出口就是"卖产品"——把车造好，运到海外，一手交钱一手交货，交易结束。但宇通模式不一样，它不仅仅是卖车，而是帮助海外国家建立本地的客车制造能力。
>
> 具体怎么做呢？宇通在十几个国家推行KD（Knock Down）组装模式。所谓KD组装，就是宇通把客车的零部件运到目标国家，然后当地的工厂按照宇通的技术标准和工艺流程进行组装...
>
> 这个模式的意义在于：它让宇通在海外市场的竞争壁垒从"产品性价比"升级为"生态系统控制力"。竞争对手可以模仿产品，但很难复制一整套技术输出体系。

**Bad — bullet, too compressed:**
> **"宇通模式"正式提出：**
> - "独创中国制造出口的'宇通模式'"
> - "成为中国汽车工业由产品输出走向技术输出的典范"
> - 从卖产品→建立本地客车制造能力（KD组装）

**When bullets ARE appropriate:** Data tables, metric inventories, step-by-step procedures, risk summary tables, competitive landscape matrices — anywhere content is factual/sequential rather than analytical.

---

## Appendices (Optional)

After Section 6, include appendices for supplementary analysis that enriches the main report without cluttering the core sections.

### When to add appendices:
- **Cross-market comparison** — When the target company faces structural headwinds (population decline, technology disruption, regulatory shifts, etc.) that have already played out in analogous markets. E.g., comparing a Chinese consumer company against Japanese peers who faced the same trends 20-30 years earlier. This is one of the most valuable supplementary analyses — it transforms abstract risk discussion into concrete historical evidence.
- **Deep-dive on a specific topic** — When a single topic (e.g., M&A history, a specific subsidiary, a regulatory change) warrants more detail than fits in the main sections.
- **Data tables or methodology notes** — When the research involved complex data reconciliation or methodology that the reader may want to verify.

### Appendix formatting:
- Label as "附录A", "附录B", etc. (or "Appendix A", etc. for English reports)
- Include a brief introduction explaining why this appendix is relevant
- Use the same source citation discipline as the main report
- Keep appendices self-contained — a reader who skips them should still get the full picture from the main report
