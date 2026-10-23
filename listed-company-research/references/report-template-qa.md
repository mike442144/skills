# Listed Company Research Report — Q&A Template (Default)

**Output format: a single self-contained HTML file** (`./[code]_[name]_深度研究报告_YYYYMMDD_HHmm.html`) with charts rendered by ECharts via CDN.

**Design logic:** 固定章节式模板的章节顺序会稀释每家公司真正的故事。Q&A 格式把公司最尖锐的问题放在最前、按重要性排序。但它绝不能变成"给旧章节标题加个问号"——每个问题的检验标准是："投资者真的会问这个问题吗？" SKILL.md 的通用规则（数据时效、归因纪律、估算透明、视角保留、表格完整性）全部照常适用。

---

## Report Metadata

HTML 文档顶部：报告标题、研究日期、数据截至范围、免责声明。免责声明表述："本报告仅为信息与教育目的，不构成任何投资建议、评级或推荐。信息均来自公开渠道，作者不保证其完整性与准确性。"

---

## 第0章 速览

Chapter 0 is deliberately slim — basic reference info only:

1. **基本信息表** — 公司全称（中英文）、交易所/代码、所属行业（注明分类体系）、总部、成立/上市时间、实际控制人、总股本。
2. **股权结构与治理** — 按上市市场披露口径：A股列前十大股东表（排名/股东/持股比例/持股数/股东性质/质押冻结与近期增减变动），港股列 5% 以上主要股东及控股架构，美股列机构持股与内部人持股；附实控人与一致行动关系、特殊股东（国家队/社保/险资/知名牛散等）、治理信号（质押、减持、回购专户）。表后配 2–4 句控制权集中度与治理评价。
3. **核心财务一览** — one compact table: 近3年+LTM 的营业收入、归母净利润、扣非、毛利率、净利率、ROE、经营现金流。不含分部、产销量等明细。
4. **估值快照** — 最新价、总市值、PE(TTM)、PB、股息率、52周区间（如可得）、快照日期。

---

## Q&A Body

### Question design rules (read before drafting)

- **Company-specific questions come first.** Derive 2–4 questions from the SKILL.md Step 3 trigger table entries that fired for this company (e.g., new controlling shareholder → "新股东入主后治理改善了吗？"; pharma + VBP exposure → "集采对收入结构的冲击量化到多少？"). These open the Q&A body under a `焦点问题` section.
- **Core question bank (below) guarantees coverage.** It replaces the checklist function of a fixed chapter template. Answer every core question that applies; omit with a one-line note only when genuinely irrelevant; add company-specific questions wherever the story demands (e.g., an ownership/governance question when the structure is unusual).
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
4. 护城河是什么、能守多久、在加强还是被侵蚀？**本问必须输出完整护城河评级：**
   - **七维评分表**：规模/成本优势、网络效应、转换成本/客户粘性、监管牌照/合规、品牌/声誉、技术/数据优势、渠道优势——每维 🟢强 / 🟡中 / 🔴弱 / ➖无 + 具体证据；没有的维度标 ➖，不要硬凑。
   - **Overall Moat Rating**：以"超额收益（ROIC > WACC）能维持多久"为锚定：🟢🟢 Exceptional（>30年，多维互强化）/ 🟢 Wide（20–30年，至少一维强、其余至少中）/ 🟡 Narrow（10–20年，优势有明确边界）/ 🟠 Emerging 或 Eroding（不确定或 <10年，地位在变动中）/ 🔴 None（<10年或从不存在）。输出格式：`**Overall Moat Rating：[emoji] [级别] — 预期持续期 [区间]**` + 2–3 句理由（具体到该公司，不写模板套话）。
   - **Moat Trajectory**：`⬆️ 加强 / ➡️ 稳定 / ⬇️ 减弱` + 1–2 句驱动因素；区分周期性逆风与结构性侵蚀，评级为稳定时注明值得监控的预警信号。
5. 有没有已经在兑现的侵蚀力量？（区分：遥远理论风险 / 早期技术监管变化 / 已发生的竞争入侵）

### Theme 3: 管理层可信吗？

Core question bank:

1. 管理层怎么解释最近的业绩？归因可信吗？（与Theme 2的独立判断交叉验证；警惕自利性偏差；注意管理层没说什么）
2. 未来3–5年打算做什么？哪些是承诺、哪些是说辞？（量化目标用表格呈现，列：战略目标/指标/目标值/时限/当前进展；区分 "we will" / "we expect" / "we aim to"）
3. 过去说的话兑现了吗？——**Credibility Scorecard 表**：列 = 过往表态（年份+出处）/ 目标 / 状态（✅达成 / ⚠️部分 / ❌未达 / ⏳进行中）/ 实际结果 / 管理层对 miss 的解释。表后 2–4 句总体评价：over-promise 还是 under-promise、是否频繁转向、如何面对 miss。若做过多年 MD&A review（见 `references/multi-year-mda-review.md`），直接以其 Strategic Timeline 与 Guidance vs Actuals 输出为底，不要临时零散搜集。
4. 资本配置创造价值还是毁灭价值？——用 5 年以上现金流、资本开支、并购、分红回购的**实际结果**（而非管理层叙事）评估，选 2–3 个与该公司故事最相关的维度：
   - CAPEX 周期与投资回报（投资强度、顺/逆周期择时、项目 ROIC vs WACC、烂尾工程检测）——重资产公司
   - 并购记录（重大交易事后结果、商誉减值史、频繁收购 vs 纪律买家）——有并购史的公司
   - 股东回报一致性（分红可持续性、回购择时、TSR 对比同业）——成熟期公司
   - 资产负债表管理（杠杆轨迹、融资择时）——高杠杆或经历过压力的公司
   - 资本纪律信号（FCF 转化、冗余现金行为、增长不计代价、关联资金流）——所有公司的交叉检查

   未选维度一句话说明为何不相关。输出：`**Capital Allocation Rating：[🟢 价值创造 / 🟡 中性 / 🔴 价值毁灭]**` + 2–4 句：以最有代表性的一笔决策作证据（具体到金额与结果，如"2019 年周期顶部投 X 亿建厂，2024 年利用率仅 55%"），区分能力与运气，注意拐点（换 CFO、战略转向）。

### Theme 4: 什么会证伪这个逻辑？

Core question bank:

1. 如果只能担心三件事，担心什么？（按概率×影响排序；每条具体到可验证的触发条件，不写通用风险）
2. 哪些风险已经在兑现？领先信号是什么？
3. 公司的缓释措施是什么、够不够？

Close with a **risk summary table**：列 = 风险类别 / 关键风险 / 影响（🔴高 / 🟡中 / 🟢低）/ 可能性 / 缓释措施。风险要具体可量化（"前三大客户流失则收入 −30%"，而非"存在客户集中风险"）；只列真实存在的风险，不凑所有上市公司通用的风险；风险确实温和就直说。

---

## HTML 技术规范

- 单一自包含 `.html` 文件；ECharts 通过 CDN 引入：`https://cdn.jsdelivr.net/npm/echarts@5/dist/echarts.min.js`。
- 每个图表一个容器 div + 一段初始化 script；加 window resize 自适应。配色用中性研究报告色系（深蓝/青/灰），不用花哨渐变。
- 中文排版：max-width 约 900px 居中，中文字体栈，行高约 1.7；h1 报告题 / h2 章 / h3 问题。表格用轻量边框样式。
- 每个图表/表格正下方一行 `<p class="source">来源：…</p>` caption。
- 页首放目录锚点链接（焦点问题/四主题/局限），便于跳转。
- 打印友好（@media print 基本适配），不依赖任何后端。
- **交付前必须在浏览器实际打开渲染验证**：确认 ECharts CDN 加载成功、每张图生成了 canvas、表格数量与来源 caption 数量符合预期——HTML/JS 错误在文本审阅中不可见，且图表是静默失败（整块消失而不报错），只看源码发现不了。

## Follow-up Questions (Living Document)

When the user asks follow-up questions after initial delivery:

1. Answer with sourced research as usual.
2. Insert as a new question under the matching theme in the HTML with the next sequential number and a date stamp, e.g. `Q14. ……（2026-11-02 追加）`. If no theme fits, place it in a final `追加问答` section. 新增图表同样遵守"图表入答、来源在下方"规则。
3. If the follow-up's findings contradict earlier answers, update the affected answers and Chapter 0 — consistency rules from SKILL.md Step 5 apply.
4. Batch data collection before patching, per SKILL.md.

---

## Limitations

报告末尾以一节披露局限，内容包括：关键信息缺口（哪些数据未能获取）、数据时效（各部分数据分别截至何时）、估算与第三方口径（哪些数字是券商测算 / 媒体口径 / 本报告推算，其假设与精度边界是什么）、未经一手来源验证的结论。来源已在正文按引用规则标注，不再单列来源清单。
