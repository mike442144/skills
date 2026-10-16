# Listed Company Research Report — Q&A Template (Default)

**Output format: a single self-contained HTML file** (`./[code]_[name]_深度研究报告_YYYYMMDD_HHmm.html`) with charts rendered by ECharts via CDN. The classic Markdown alternative is `report-template.md` (used when the user requests a traditional structure or a side-by-side comparison).

**Design logic:** The classic template's fixed chapter order dilutes each company's real story. The Q&A format fixes this by putting the company's sharpest questions first and ordering everything by materiality. What it must NOT become: the old chapter headings with question marks appended. The test for every question is "would an investor actually ask this?"

---

## Report Metadata

At the top of the HTML document: title, research date, data-as-of scope, disclaimer (same wording as the classic template).

---

## 第0章 速览与评级

Chapter 0 is deliberately slim — it holds **basic reference info and verdicts only**:

1. **基本信息表** — same as classic template §1.1.
2. **核心财务一览** — one compact table: 近3年+LTM 的营业收入、归母净利润、扣非、毛利率、净利率、ROE、经营现金流。不含分部、股东、产销量等明细。
3. **护城河评级表 + 总评级 + 轨迹** — rating tables and output format from classic template §3.4. The verdict sits here; the reasoning lives in the Theme 2 answer.
4. **资本配置评级** — verdict + 2-3 sentence rationale (format from classic template §4.4). The reasoning lives in the Theme 3 answer.
5. **估值快照** — 最新价、总市值、PE(TTM)、PB、股息率、52周区间（如可得）、快照日期。

**Rule:** 数据图表（股权结构、分部收入、产销量、中标价、费用趋势、季度序列等）不集中放在第0章，全部放入支撑它们的对应问答中。

---

## Q&A Body

### Question design rules (read before drafting)

- **Company-specific questions come first.** Derive 2–4 questions from the SKILL.md Step 3 trigger table entries that fired for this company (e.g., new controlling shareholder → "新股东入主后治理改善了吗？"; pharma + VBP exposure → "集采对收入结构的冲击量化到多少？"). These open the Q&A body under a `焦点问题` section.
- **Core question bank (below) guarantees coverage.** It replaces the classic template's checklist function. Answer every core question that applies; omit with a one-line note only when genuinely irrelevant; add company-specific questions wherever the story demands (e.g., an ownership/governance question when the structure is unusual).
- **Order by materiality within each theme.** The question whose answer most changes the investment understanding goes first in its theme. Do not default to the bank's listed order.
- **Bad vs. good questions:**
  - Bad: "客户与收入模式是什么？" — a template heading with a question mark.
  - Good: "谁在为公司付钱？他们为什么不换供应商？" — a question an investor would ask, demanding a position.

### Answer structure (master pattern)

- **结论先行 → 事实论据与推理逻辑。** Each answer opens with a bolded one-sentence conclusion, then presents evidence and reasoning. 叙述性散文、图表、要点列表都是合法表达手段——唯一禁止的是"只有结论的 bullet 堆叠"（高度压缩、读者无法理解来龙去脉的写法）。
- **图表放入对应回答。** 时间序列、构成演变、多维对比类信息优先用图表呈现；三个数字以内的小事实直接写进句子即可，不必作图。表格用于结构化枚举（股东、中标价、评分卡、风险汇总等）。
- **引用规则：** 叙述性文本中的数据，来源内联在句中（如"（来源：2025 年报）"）；图表或表格的来源标注在紧贴其下方的一行（caption 样式）。两者不可混用位置。
- **Each answer must stand alone** — a reader jumping to one question from the table of contents gets the full picture without reading the whole report. Cross-reference with `(见 QX)` where answers genuinely interlock.
- **Numbering:** questions are numbered Q1, Q2… in order of creation. Follow-up questions (see below) get the next number and are inserted under the matching theme, so position and number may diverge over time — that is expected and shows the document's history.

### 焦点问题 (Company-Specific Questions)

2–4 questions derived from trigger conditions, each answered at full depth. This section is the report's front page — it answers why THIS company, THIS moment, is worth understanding.

### Theme 1: 这门生意是什么？

Core question bank:

1. 这家公司靠什么赚钱？（一句话商业模式；钱从谁手里、以什么机制收上来）
2. 收入结构过去几年怎么演变？增长引擎和现金牛各是哪个？（分部收入构成图通常放这里）
3. 每个重要分部一问：这个业务的商业模型是什么？客户为什么付钱、粘性来自哪？关键投入或瓶颈是什么？（重资产业务详写产能/供应链，轻资产业务写人才/牌照/数据，不套制造业框架）
4. 哪些运营指标最能反映这门生意的健康度？最新读数是多少？（3–5个，不是KPI堆砌）
5. 分部之间有没有真实的协同，还是拼盘？

### Theme 2: 竞争环境如何？

Core question bank:

1. 这个行业有多大、还在增长吗？增长驱动和结构性约束是什么？（市场规模/渗透率图表放这里）
2. 格局集中还是分散？公司排第几、真正在和谁竞争？（竞争格局表/对比图放这里）
3. 公司凭什么赢（或为什么在输）？差异化具体在哪？
4. 护城河是什么、能守多久、在加强还是被侵蚀？（此处展开第0章护城河评级的推理；区分周期性逆风与结构性侵蚀）
5. 有没有已经在兑现的侵蚀力量？（区分：遥远理论风险 / 早期技术监管变化 / 已发生的竞争入侵）

### Theme 3: 管理层可信吗？

Core question bank:

1. 谁在控制这家公司？股权结构与治理如何？（股权结构表放这里；实控人关系、特殊股东、治理信号）
2. 管理层怎么解释最近的业绩？归因可信吗？（与Theme 2的独立判断交叉验证；警惕自利性偏差；注意管理层没说什么）
3. 未来3–5年打算做什么？哪些是承诺、哪些是说辞？（量化目标用表格；区分 "we will" / "we expect" / "we aim to"）
4. 过去说的话兑现了吗？（Credibility Scorecard，格式见 classic template §4.3；有多年MD&A review时直接以其输出为底）
5. 资本配置创造价值还是毁灭价值？（此处展开第0章资本配置评级的推理；选2–3个最相关维度，方法见 classic template §4.4；用具体数字，区分能力与运气）

### Theme 4: 什么会证伪这个逻辑？

Core question bank:

1. 如果只能担心三件事，担心什么？（按概率×影响排序；每条具体到可验证的触发条件，不写通用风险）
2. 哪些风险已经在兑现？领先信号是什么？
3. 公司的缓释措施是什么、够不够？

Close with a risk summary table (format: classic template §5.X).

---

## HTML 技术规范

- 单一自包含 `.html` 文件；ECharts 通过 CDN 引入：`https://cdn.jsdelivr.net/npm/echarts@5/dist/echarts.min.js`。
- 每个图表一个容器 div + 一段初始化 script；加 window resize 自适应。配色用中性研究报告色系（深蓝/青/灰），不用花哨渐变。
- 中文排版：max-width 约 900px 居中，中文字体栈，行高约 1.7；h1 报告题 / h2 章 / h3 问题。表格用轻量边框样式。
- 每个图表/表格正下方一行 `<p class="source">来源：…</p>` caption。
- 页首放目录锚点链接（焦点问题/四主题/来源），便于跳转。
- 打印友好（@media print 基本适配），不依赖任何后端。

## Follow-up Questions (Living Document)

When the user asks follow-up questions after initial delivery:

1. Answer with sourced research as usual.
2. Insert as a new question under the matching theme in the HTML with the next sequential number and a date stamp, e.g. `Q14. ……（2026-11-02 追加）`. If no theme fits, place it in a final `追加问答` section. 新增图表同样遵守"图表入答、来源在下方"规则。
3. If the follow-up's findings contradict earlier answers, update the affected answers and Chapter 0 verdicts — consistency rules from SKILL.md Step 5 apply.
4. Batch data collection before patching, per SKILL.md.

---

## Sources & Limitations

Same as classic template §6 (skills used table, other sources, limitations). This section follows the Q&A body, before any appendices.

## Length

Target 3,000–8,000 字正文（不含图表与表格）。A focused 4,000-word report beats a padded 8,000-word one — depth follows relevance. All universal rules from SKILL.md (freshness, attribution discipline, estimation transparency, perspective retention, table completeness) apply unchanged.
