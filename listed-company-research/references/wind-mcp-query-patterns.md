# Wind MCP 上市公司研究查询模板

Proven query patterns for using `wind-mcp-skill` to collect data for listed company research reports. All commands run from the wind-mcp-skill directory: `cd /home/mike/skills/wind-mcp-skill`.

## A股标的格式

Wind code: `600079.SH` (上海), `000001.SZ` (深圳), `688xxx.SH` (科创板), `8xxxxx.BJ` (北交所)

## 1. 前十大股东 (Equity Holders)

```bash
# 最新季报前十大股东
node scripts/cli.mjs call stock_data get_stock_equity_holders '{"question":"600079.SH2026年一季报前十大股东持股比例持股数量"}'

# 特定报告期
node scripts/cli.mjs call stock_data get_stock_equity_holders '{"question":"600079.SH2025年年报前十大股东"}'

# 实际控制人
node scripts/cli.mjs call stock_data get_stock_equity_holders '{"question":"600079.SH实际控制人名称"}'

# 股本结构
node scripts/cli.mjs call stock_data get_stock_equity_holders '{"question":"600079.SH总股本流通股本限售股"}'
```

**返回字段：** 股东名称、持股比例(%)、持股数量(股)、名次。注意NL工具返回的quarter数据格式为 `YYYY年X季报` 或 `YYYY年年报`。

## 2. 行情快照 (Price Indicators)

先查 `references/indicators.md` 确认中文字段名。

```bash
node scripts/cli.mjs call stock_data get_stock_price_indicators '{"windcode":"600079.SH","indexes":"中文简称,最新成交价,今日开盘价,今日最高价,今日最低价,前收盘价,成交量,成交额,涨跌,涨跌幅,换手率,总市值1,流通市值,市盈率(TTM),市净率,股息率,52周最高,52周最低"}'
```

**常用indexes候选（须去indicators.md核对）：**
- 行情：`中文简称,最新成交价,前收盘价,今日开盘价,今日最高价,今日最低价,成交量,成交额,涨跌,涨跌幅`
- 估值：`总市值1,流通市值,市盈率(TTM),市净率,股息率`
- 交易：`换手率,量比,委比,涨停价,跌停价`
- 区间：`52周最高,52周最低`

## 3. 财务数据 (Fundamentals)

NL入参，question字段**禁止空格**。

```bash
# 年报核心数据
node scripts/cli.mjs call stock_data get_stock_fundamentals '{"question":"600079.SH2025年年报营业收入归母净利润扣非净利润经营现金流"}'

# 季报数据
node scripts/cli.mjs call stock_data get_stock_fundamentals '{"question":"600079.SH2026年一季报营收净利润毛利率ROE每股收益"}'

# 同比增速
node scripts/cli.mjs call stock_data get_stock_fundamentals '{"question":"600079.SH2026年一季报营收同比净利润同比"}'

# 估值指标
node scripts/cli.mjs call stock_data get_stock_fundamentals '{"question":"600079.SH最新总市值流通市值市盈率市净率股息率"}'
```

**返回格式：** 数字通常带单位（亿元/元/%），注意区分"年报"vs"一季报/中报/三季报"。同比增速返回百分比数值。

### 分红/DPS数据 (Dividends & Splits)

```bash
# 多年DPS + 转增 + 红股（用于split-adjusted DPS分析）
node scripts/cli.mjs call stock_data get_stock_fundamentals '{"question":"600066.SH2015到2025年每股股利每股分红派息转增比例"}'
```

**返回字段：** `每股税前股利`（元）、`每股红股`（股）、`每股转增股本`（股）。每行对应一个报告期（Q4 FY2015等）。

**用途：** 检测历史转增/送股（如10转6），对DPS做追溯调整以保持与EPS的可比性。如果`每股红股`或`每股转增股本`非零，说明该年度发生了股本扩张，之前的DPS需要除以(1+送转比例)来调整。

**✅ 已验证：** 宇通客车600066.SH 2015-2025年查询成功返回11行数据。

## 4. 公司公告 (Announcements)

```bash
# 最新公告
node scripts/cli.mjs call financial_docs get_company_announcements '{"query":"600079.SH 2026年最新公告","top_k":5}'

# 特定类型公告
node scripts/cli.mjs call financial_docs get_company_announcements '{"query":"600079.SH 定增预案","top_k":3}'

# 年报MD&A（经营情况讨论与分析）— 获取管理层讨论、业绩驱动因素、费用分析
node scripts/cli.mjs call financial_docs get_company_announcements '{"query":"688271.SH 2025年年报 经营情况讨论与分析","top_k":3}'

# 年报风险因素 — 获取公司自述的风险章节
node scripts/cli.mjs call financial_docs get_company_announcements '{"query":"688271.SH 风险因素 关税 海外 贸易限制 2025年年报","top_k":3}'

# 年报全文产品/销售拆分 — 当分产品收入查询返回空时，用此从年报正文提取
node scripts/cli.mjs call financial_docs get_company_announcements '{"query":"688271.SH 2025年年报 产品 销售","top_k":5}'
```

**⚡ 高价值模式 — 公告正文提取替代web_search：** `get_company_announcements` 返回的 `content` 字段包含公告原文片段（非仅标题/链接）。对年报类公告，用关键词（"经营情况讨论与分析"/"风险因素"/"产品 销售"/"主要控股参股公司分析"）+ `top_k=3~5` 可以拿到年报核心章节的原文，包含财务表格数据、管理层论述、风险描述。这是比web_search更可靠的一手数据来源。联影医疗案例中，通过 `"2025年年报 经营情况讨论与分析"` 拿到了完整的分产品线收入明细表（CT/MR/MI/XR/RT各线收入及同比增速）、管理层对费用率变化的详细解释、以及完整的风险因素章节。

## 5. 港股/美股

港股用 `global_stock_data` server，美股同理。**港股代码必须去掉前导零**：`01810.HK` → `1810.HK`，`02858.HK` → `2858.HK`。带前导零的代码会导致 `QUERY_FAILED`。

```bash
# 港股股东（注意：去前导零）
node scripts/cli.mjs call global_stock_data get_global_stock_equity_holders '{"question":"1810.HK主要股东、控股股东"}'

# 港股财务
node scripts/cli.mjs call global_stock_data get_global_stock_fundamentals '{"question":"小米集团1810.HK2025年ROE和营收"}'

# 港股行情（注意：字段名必须用 indicators.md 中的精确名称）
node scripts/cli.mjs call global_stock_data get_global_stock_price_indicators '{"windcode":"1810.HK","indexes":"中文简称,最新成交价,涨跌幅,市盈率(TTM),市净率(LF),52周最高,52周最低"}'
```

**⚠️ Pitfall — 港股股东数据不完整：** `get_global_stock_equity_holders` 对港股仅返回股东名称和排名（`主要股东名称名次`），**不返回持股比例和持股数量**。这与A股的 `get_stock_equity_holders`（返回持股比例%、持股数量股）完全不同。获取港股股东持股比例需通过：
1. 港交所权益披露公告（HKEX Disclosure of Interests）— 5%以上股东
2. 年报"Substantial Shareholders"章节（`get_company_announcements` 检索）
3. 同花顺等第三方数据平台（web_search）
4. 公司年报中的控股股东架构图（含信托/控股链条）

**⚠️ Pitfall — 港股行情指标字段校验同样严格：** `get_global_stock_price_indicators` 的 `indexes` 字段必须使用 `references/indicators.md` 中的精确字段名。已验证的错误：`市盈率`（报 INVALID_PARAM_VALUE）→ 应为 `市盈率(TTM)`；`总市值` → 应为 `总市值1`。建议对港股也用 `get_global_stock_fundamentals` 查估值指标（更稳定），仅在需要盘中行情快照时用 price_indicators。

**✅ 已验证案例 — 小米集团 1810.HK：** 基本信息、多年财务数据（2022-2025）、分产品收入（5大板块）、2026年Q1数据均成功返回。公告内容（年报MD&A/风险因素/董事长致辞）通过 `financial_docs.get_company_announcements` 成功获取。

## 6. 分产品/分地区收入 (Segment Revenue)

NL入参，尝试从年报或财报附注中提取各产品线/地区收入明细：

```bash
# 分产品收入
node scripts/cli.mjs call stock_data get_stock_fundamentals '{"question":"600079.SH2025年年报分产品收入各业务收入构成毛利率"}'

# 分地区收入（国内/海外拆分）
node scripts/cli.mjs call stock_data get_stock_fundamentals '{"question":"600066.SH2025年年报分地区收入各业务毛利率收入占比"}'
```

**✅ 已验证有效案例（分产品 — 联影医疗 688271.SH）：** 查询返回了详细的分产品线数据，包含以下列：
- `主营构成_按产品_项目名称`（如"销售医学影像诊断设备及放射治疗设备"、"提供维修收入"、"软件收入"）
- `项目收入`（亿元）
- `项目毛利率`（%）
- `项目收入占比`（%）
- `名次`（排序）

**✅ 已验证有效案例（分地区 — 宇通客车 600066.SH）：** 分地区查询返回：
- `主营地区名称`（如"海外"/"国内"/"其他业务(地区)"）
- `主营地区收入`（亿元）
- `主营地区毛利率`（%）
- `主营地区收入占比`（%）

对出海型企业尤其有用，可直接拿到海外/国内的收入和毛利率拆分。

**⚠️ Pitfall:** Wind MCP的NL工具对分产品收入数据的覆盖**因标的而异**，部分公司能返回详细数据（含收入、毛利率、收入占比），部分公司仅返回汇总数据或空。正确做法：先尝试此查询，若返回有效数据（rows非空且项目名称不为null）则直接使用；若返回空或仅有合计行，则必须通过 `get_company_announcements` 检索年报全文（`top_k`设大，如5-10），从年报正文/附注中手动提取分产品收入。也可直接搜索券商研报（via `mx-finance-search`）获取产品拆分数据。

**⚠️ Pitfall — 分产品收入查询返回null-padded行：** Wind MCP分产品收入查询固定返回10行（rows数组长度=10），但有效数据可能只有3-4行。后续行的项目名称(project_name)、收入、毛利率均为null。**过滤规则**：只保留项目名称不为null的行。返回数据示例：第1-4行为有效产品（血液净化类/给药器具类等），第5-10行全部为null。

**⚠️ Pitfall — 投资收益/公允价值变动明细：** Wind MCP 的 `get_stock_fundamentals` 对"投资收益""公允价值变动""信托理财""股票投资"等细项查询经常返回 `QUERY_FAILED`。这是 Wind NL 解析器的覆盖盲区，不是 Key 或网络问题。不要重试。正确做法：通过 `get_company_announcements` 检索年报全文从附注提取，或在业绩说明会 Q&A 中寻找管理层回应。

**⚠️ Pitfall — 费用项单位不一致 & 比率重复：** `get_stock_fundamentals` 批量查询费用项时，不同行项目可能返回不同单位。宇通客车600066案例（2025年年报）：研发费用/销售费用/管理费用返回亿元（18.08/14.23/8.35），但财务费用返回的值（42.14）实际单位为百万元（= 0.42亿元），与年报MD&A的4,214.25万元吻合，但与其他费用项差100倍。同时，"销售费用占营业收入比例"和"研发费用占营业收入比例"返回了相同的值（4.36%），实际销售费用率应为3.44%（142,337/4,142,617）。**处理规则：** 费用类数据必须与年报MD&A"利润表及现金流量表相关科目变动分析表"交叉验证，不信任Wind返回的单位标注和比率计算。

**⚠️ Pitfall — 公告RAG可能返回空结果：** `get_company_announcements` 对部分股票的所有查询均返回空数组，即使是年报类查询。宇通客车600066案例：4个不同关键词查询（经营情况讨论与分析、风险因素、主要控股参股公司分析、利润分配方案）全部返回空。这不同于"覆盖因标的而异"的分产品收入问题——是整个RAG索引对该股票可能未建索引。**处理规则：** 若首次查询返回空，不要重试其他关键词组合（浪费时间）；直接改用 cninfo 年报PDF下载 + pymupdf提取MD&A，或 web_search 搜索券商研报补充。

**⚠️ Pitfall — CIQ Google Sheets 补充：** 用户维护的 CIQ 财务 Google Sheets 是极高价值的长周期数据源（10-20 年历史）。当 Wind/mx 免费层仅支持 3 年回溯时，优先检查 Google Sheets（通过 `google-workspace` skill）。注意：CIQ 数据单位为 CNY MN（百万元），毛利率/ROE 等为小数格式（0.372 = 37.2%）。Tab 名通常为"公司简称+财务"。详见 `gs-financial-structure.md`。

**⚠️ Pitfall — 极端异常值必须交叉验证（会计差错更正检测）：** 当 `get_stock_fundamentals` 返回的多年数据出现极端不连续（如某年营收/净利润同比骤降40%以上，且公司仍在正常经营），**立即**用 `get_company_announcements` 搜索 `"前期会计差错更正"` 或 `"会计政策变更"`。这是数据质量的第一道关卡——更正后的数据可能让一家正常公司看起来"崩盘"了。五粮液案例：Wind返回2025年营收405亿元（-54.5% YoY），看似经营崩溃，实际上是收入确认口径从"发货即确认"改为"终端动销完成后确认"导致的历史重述。**处理规则：** 发现极端异常值时，绝不能直接写入报告；先查公告确认是真实经营恶化还是会计口径变更，再决定如何呈现。详见 SKILL.md 中的 "Accounting restatement detection and analysis" pattern 和 `references/accounting-restatement-analysis.md`。

## 7. 批量财务数据抓取（多家公司）

当需要同时抓取多家公司的年度财务数据时，使用以下批量模式。返回的是**跨年度聚合列**（如"2021到2025年营业收入"），不是逐年的单列。

```python
# Python批量模式（推荐）
import json, subprocess

companies = [
    ("华新水泥", "600801.SH"),
    ("冀东水泥", "000401.SZ"),
    ("天山股份", "000877.SZ"),
]

all_results = {}
for name, code in companies:
    question = f"获取{name}{code} 2021-2025年营业收入、归母净利润、毛利率、ROE、资产负债率、经营现金流"
    cmd = f'''cd /home/mike/skills/wind-mcp-skill && node scripts/cli.mjs call stock_data get_stock_fundamentals \'{{"question":"{question}"}}\' 2>/dev/null'''
    # 解析嵌套JSON: outer → content[0].text → inner
    outer = json.loads(subprocess.check_output(cmd, shell=True))
    inner = json.loads(outer['content'][0]['text'])
    rows = inner['data']['data'][0]['rows']
    cols = inner['data']['data'][0]['columns']
    col_names = [c['name'] for c in cols]
    annual_rows = [r for r in rows if 'Q4' in str(r[2])]
    all_results[name] = {'columns': col_names, 'data': annual_rows}
```

**返回列名模式：** `2021到2025年营业收入`, `2021到2025年归母净利润`, `2021到2025年销售毛利率`, `2021到2025年净资产收益率ROE`, `2021到2025年资产负债率`, `2021到2025年经营活动产生的现金流量净额`。每行对应一个报告期（Q4 FY2021等），按时间排序后即为年度序列。

**港股批量：** 用 `global_stock_data get_global_stock_fundamentals`，股票代码用 `3323.HK` 格式（有时需要去掉前导零，`03323.HK` → `3323.HK`）。`analytics_data` 对港股经常返回失败，不要fallback到它。

**⚠️ Pitfall — Wind JSON嵌套解析：** Wind CLI返回的是双层嵌套JSON。外层 `{"content": [{"type": "text", "text": "{...inner JSON...}"}]}`，必须先 `outer['content'][0]['text']` 取内层字符串，再 `JSON.parse`。直接解析外层拿不到数据。

1. **question字段禁止空格**：`"600079.SH 2026年一季报"` → `"600079.SH2026年一季报"`
2. **NL工具季度命名**：一季报/中报/三季报/年报，非Q1/Q2/Q3/Annual
3. **单工具单标的**：不能一次查多个股票代码
4. **数据单位**：财务数据通常返回"亿元"，股东持股返回"股"，比例返回"%"
5. **NL vs 结构化**：优先用NL工具（`get_stock_fundamentals`, `get_stock_equity_holders`），仅在已知具体字段名时用`get_stock_price_indicators`（须核对indicators.md）
6. **`get_stock_price_indicators` 可靠性**：该端点在部分科创板标的上可能返回 `QUERY_FAILED: 未获取到有效行情数据` 错误。**推荐做法**：用 `get_stock_fundamentals` 查估值指标（总市值、PE、PB等），它比 `get_stock_price_indicators` 更稳定。仅在需要盘中行情快照（涨跌幅、换手率、52周区间等）时才用 `get_stock_price_indicators`，且要做好fallback准备。
7. **数据收集顺序建议**：先查公司基本信息 → 财务数据（多年度+最新季报）→ 股东信息 → 公告 → 最后用web_search补充行业/竞争格局/产品拆分信息。不要把web_search作为第一手段。
8. **合同负债（预收款）作为先行指标**：对设备类公司（医疗器械、工业设备等），`get_stock_fundamentals` 查询"合同负债"或"预收账款"可获得在手订单规模。联影医疗案例中，合同负债29.75亿元（+39.1%YoY）是未来收入的领先指标。建议纳入标准查询清单。
9. **资产负债表关键项查询模板**：`688271.SH2025年年报应收账款存货商誉资产负债率总资产净资产` — 一次查询可拿到多个BS关键指标，用于风险评估章节。

10. **global_stock_data 对港股金融公司的局限性**：`global_stock_data` server for HK/US stocks is less reliable than `stock_data` for A-shares. Common failure modes: (1) Returns QUERY_FAILED for valid queries — try dropping leading zeros from stock codes (e.g., `02858.HK` → `2858.HK`). (2) Historical multi-year queries may return null for some metrics — fall back to Google Sheets + web search. (3) Some metrics (拨备覆盖率, 逾期率, 不良贷款率) are NOT available through wind-mcp for HK stocks — use credit rating reports and annual report appendices instead. Always have a fallback plan when researching HK-listed financial companies.
