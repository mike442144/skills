# 行业数据源参考指南

## 一、官方统计数据

### 1.1 国家统计局

- **网址**: https://data.stats.gov.cn/
- **数据**: GDP、工业增加值、固定资产投资、消费数据、进出口
- **使用**: 搜索"行业名称 + 统计数据"或直接访问分行业数据

### 1.2 各部委官网

| 部委 | 相关数据 |
|------|---------|
| 工信部 | 工业运行数据、行业产量、企业效益 |
| 发改委 | 宏观经济、产业政策、项目审批 |
| 商务部 | 贸易数据、外资利用、消费数据 |
| 海关总署 | 进出口细分产品数据 |
| 能源局 | 能源产量、消费、装机数据 |
| 农业农村部 | 农业产量、农产品价格 |

### 1.3 行业协会

- **查找方式**: 搜索"[行业名称] + 协会"
- **数据**: 行业产量、销量、企业数量、产能利用率
- **示例**: 中国汽车工业协会、中国光伏行业协会、中国半导体行业协会

---

## 二、上市公司数据

### 2.1 财报数据

- **巨潮资讯**: http://www.cninfo.com.cn/
- **东方财富**: https://www.eastmoney.com/
- **同花顺**: https://www.10jqka.com.cn/

### 2.2 行业对比分析

通过 `listed-company-research` 技能研究行业内主要公司,提取:
- 营收规模与增速
- 毛利率、净利率
- 市场份额估算
- 业务结构

### 2.3 招股说明书

- **价值**: 招股书通常包含详细的行业分析、市场规模、竞争格局
- **来源**: 巨潮资讯、港交所披露易、SEC EDGAR

---

## 三、第三方研究机构

### 3.1 国际咨询机构

| 机构 | 特点 |
|------|------|
| McKinsey | 战略洞察、行业趋势报告 |
| BCG | 行业分析、数字化转型 |
| Deloitte | 行业洞察、监管政策 |
| PwC | 行业展望、并购趋势 |
| Gartner | 技术成熟度曲线(科技行业) |
| IDC | IT 市场数据、预测 |

### 3.2 国内研究机构

- **艾瑞咨询**: https://www.iresearch.cn/ (互联网、科技)
- **易观分析**: https://www.analysys.cn/ (数字经济)
- **头豹研究院**: https://www.leadleo.com/ (各行业)
- **前瞻产业研究院**: https://www.qianzhan.com/ (行业报告)
- **亿欧智库**: https://www.iyiou.com/ (创新科技)

### 3.3 券商研报

- **慧博投研**: http://www.hibor.com.cn/
- **萝卜投研**: https://robo.datayes.com/
- **发现报告**: https://www.fxbaogao.com/

---

## 四、全球数据源

### 4.1 国际组织

| 组织 | 数据 |
|------|------|
| 世界银行 | https://data.worldbank.org/ |
| IMF | https://www.imf.org/en/Data |
| WTO | https://www.wto.org/english/res_e/statis_e/statis_e.htm |
| OECD | https://data.oecd.org/ |

### 4.2 全球市场研究

- **Statista**: https://www.statista.com/ (全球统计数据)
- **Bloomberg**: https://www.bloomberg.com/professional/ (金融数据)
- **Market Research Future**: https://www.marketresearchfuture.com/

---

## 五、搜索技巧

### 5.1 中文搜索关键词模板

```
[行业名称] 市场规模 2024
[行业名称] 行业报告 深度分析
[行业名称] 产业链 上下游
[行业名称] 竞争格局 市场份额
[行业名称] 政策 最新
[行业名称] 发展趋势 前景
```

### 5.2 英文搜索关键词模板

```
[Industry name] market size 2024
[Industry name] industry report analysis
[Industry name] value chain
[Industry name] competitive landscape market share
[Industry name] growth forecast CAGR
[Industry name] trends outlook 2025
```

### 5.3 数据交叉验证

- 同一数据点至少从 2-3 个独立来源验证
- 优先使用官方/半官方数据
- 注意数据口径差异(如:销售额 vs 出货量 vs 装机量)
- 标注数据来源和时间戳

---

## 六、数据使用注意事项

### 6.1 常见陷阱

1. **口径不一致**: 不同机构对同一行业的定义可能不同
2. **时间滞后**: 官方数据通常有 1-3 个月滞后
3. **样本偏差**: 第三方调研样本可能不具代表性
4. **预测偏差**: 市场预测通常偏乐观

### 6.2 最佳实践

- 明确标注每个数据的来源
- 区分事实数据与分析判断
- 对矛盾数据进行说明
- 使用最新可用数据,并注明数据截止时间
