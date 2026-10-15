# Listed Company Research Report — Q&A Template (Default)

This template organizes the report as a **fact sheet + conclusion-first Q&A body**. Use it by default; fall back to `report-template.md` (classic five-chapter) when the user requests a traditional structure or a side-by-side comparison.

**Design logic:** The classic template's fixed chapter order dilutes each company's real story — every report starts with basic info and only reaches management in chapter 4. The Q&A format fixes this by putting the company's sharpest questions first and ordering everything by materiality. What it must NOT become: the old chapter headings with question marks appended. The test for every question is "would an investor actually ask this?"

---

## Report Metadata

Same header as the classic template (title, research date, data-as-of date, disclaimer).

---

## Chapter 0: Fact Sheet (事实速览)

Hard data and verdicts up front, all as tables. The Q&A body contains no original data tables of its own except small supporting ones inside answers — the fact sheet is where a reader goes to look things up.

1. **基本信息表** — same as classic template §1.1.
2. **股权结构表** — use the market-appropriate format from classic template §1.2 (A股前十大股东 / HKEX Substantial Shareholders / US 13F·13G·Insider), with its brief narrative on concentration and recent changes.
3. **分部收入构成表（近三年）** — same as classic template §2.0, including the segment profitability table when disclosed. All freshness and reclassification rules from §2.0 apply unchanged.
4. **护城河评级表 + 总评级 + 轨迹** — same rating tables and output format as classic template §3.4. Chapter 0 presents the verdict; the Q&A body (Theme 2) carries the reasoning.
5. **资本配置评级** — same output format as classic template §4.4. The verdict sits here; Theme 3 carries the reasoning.

Keep Chapter 0 tight — it is a reference layer, not the analysis. If a table grows beyond ~15 rows of narrative annotation, trim the annotation and move the reasoning into the matching Q&A answer.

---

## Q&A Body

### Question design rules (read before drafting)

- **Company-specific questions come first.** Derive 2–4 questions from the SKILL.md Step 3 trigger table entries that fired for this company (e.g., new controlling shareholder → "新股东入主后治理改善了吗？"; pharma + VBP exposure → "集采对收入结构的冲击量化到多少？"; ownership change flagged → the corresponding question). These open the Q&A body under a `## 焦点问题` section.
- **Core question bank (below) guarantees coverage.** It replaces the classic template's checklist function. Answer every core question that applies; omit with a one-line note only when genuinely irrelevant; add company-specific questions wherever the story demands.
- **Order by materiality within each theme.** The question whose answer most changes the investment understanding goes first in its theme. Do not default to the bank's listed order.
- **Bad vs. good questions:**
  - Bad: "客户与收入模式是什么？" — a template heading with a question mark.
  - Good: "谁在为公司付钱？他们为什么不换供应商？" — a question an investor would ask, demanding a position.
- **Answer format:** conclusion-first sentence → 2–4 narrative paragraphs → evidence with inline source citations. The narrative-over-bullet rule from SKILL.md applies in full. Small tables are allowed inside answers for supporting data; the primary data tables live in Chapter 0.
- **Each answer must stand alone** — a reader jumping to one question from the table of contents gets the full picture of that question without reading the whole report. Cross-reference with `(见 QX)` where answers genuinely interlock.
- **Numbering:** questions are numbered Q1, Q2… in order of creation. Within the initial report, numbering follows position. Follow-up questions (see below) get the next number and are inserted under the matching theme, so position and number may diverge over time — that is expected and shows the document's history.

### 焦点问题 (Company-Specific Questions)

2–4 questions derived from trigger conditions, each answered at full depth. This section is the report's front page — it answers why THIS company, THIS moment, is worth understanding.

### Theme 1: 这门生意是什么？

Core question bank:

1. 这家公司靠什么赚钱？（一句话商业模式；钱从谁手里、以什么机制收上来）
2. 收入结构过去几年怎么演变？增长引擎和现金牛各是哪个？（呼应第0章分部表；判断集中度与第二曲线）
3. 每个重要分部一问：这个业务的商业模型是什么？客户为什么付钱、粘性来自哪？关键投入或瓶颈是什么？（重资产业务详写产能/供应链，轻资产业务写人才/牌照/数据，不套制造业框架）
4. 哪些运营指标最能反映这门生意的健康度？最新读数是多少？（3–5个，不是KPI堆砌）
5. 分部之间有没有真实的协同，还是拼盘？

### Theme 2: 竞争环境如何？

Core question bank:

1. 这个行业有多大、还在增长吗？增长驱动和结构性约束是什么？
2. 格局集中还是分散？公司排第几、真正在和谁竞争？
3. 公司凭什么赢（或为什么在输）？差异化具体在哪？
4. 护城河是什么、能守多久、在加强还是被侵蚀？（此处展开第0章护城河评级的推理；区分周期性逆风与结构性侵蚀）
5. 有没有已经在兑现的侵蚀力量？（区分：遥远理论风险 / 早期技术监管变化 / 已发生的竞争入侵）

### Theme 3: 管理层可信吗？

Core question bank:

1. 管理层怎么解释最近的业绩？归因可信吗？（与Theme 2的独立行业判断交叉验证；警惕自利性偏差；注意管理层没说什么）
2. 未来3–5年打算做什么？哪些是承诺、哪些是说辞？（量化目标用表格；区分 "we will" / "we expect" / "we aim to"）
3. 过去说的话兑现了吗？（Credibility Scorecard，格式见 classic template §4.3；有多年MD&A review时直接以其输出为底）
4. 资本配置创造价值还是毁灭价值？（此处展开第0章资本配置评级的推理；选2–3个最相关维度，方法见 classic template §4.4；用具体数字，区分能力与运气）

### Theme 4: 什么会证伪这个逻辑？

Core question bank:

1. 如果只能担心三件事，担心什么？（按概率×影响排序；每条具体到可验证的触发条件，不写通用风险）
2. 哪些风险已经在兑现？领先信号是什么？
3. 公司的缓释措施是什么、够不够？

Close with a risk summary table (format: classic template §5.X).

---

## Follow-up Questions (Living Document)

When the user asks follow-up questions after initial delivery:

1. Answer with sourced research as usual.
2. Insert as a new question under the matching theme with the next sequential number and a date stamp, e.g. `### Q14. ……（2026-11-02 追加）`. If no theme fits, place it in a final `## 追加问答` section.
3. If the follow-up's findings contradict earlier answers, update the affected answers and Chapter 0 verdicts — consistency rules from SKILL.md Step 5 apply.
4. Batch data collection before patching, per SKILL.md.

---

## Sources & Limitations

Same as classic template §6 (skills used table, other sources, limitations). This section follows the Q&A body, before any appendices.

## Formatting

- `##` for Chapter 0, the four themes, 焦点问题, Sources; `###` for individual questions (`### Q1. ……`).
- Inline source citations `(Source: …)` on first mention, same discipline as the classic template.
- Target length: 3,000–8,000 words. A focused 4,000-word report beats a padded 8,000-word one — depth follows relevance.
- All universal rules from SKILL.md (freshness, attribution discipline, estimation transparency, perspective retention, table completeness) apply unchanged.
