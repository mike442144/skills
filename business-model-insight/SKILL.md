---
name: business-model-insight
description: >
  This skill should be used when the user wants to analyze and deconstruct a business model or commercial topic
  through a structured six-step process. It explores the business model, competitive landscape, evolution direction,
  and景气度 (prosperity) trends, then deconstructs the research report into what/why/how questions, extracts effective
  propositions, scores them, and finally produces an insightful report using analogies and accessible language.
  Triggers include phrases like "商业模式分析", "商业模式洞察", "business model analysis", "拆解商业模式",
  "研究XX行业的商业模式", or when the user provides a business topic and asks for deep business model analysis.
metadata:
  author: Mike Chen
  version: '1.0'
---

# Business Model Insight Skill

## Overview

This skill transforms a business topic into a comprehensive, structured business model insight report through a six-step process:

1. **Step 1**: Generate `商业模式研究报告.md` - Explore business model, competitive landscape, evolution direction, and prosperity trends
2. **Step 2**: Generate `商业模式研究报告解构.md` - Deconstruct the report into what/why/how questions
3. **Step 3**: Generate effective propositions with arguments from the research report
4. **Step 4**: Score and rank propositions based on coverage of what/why/how questions
5. **Step 5**: Generate linked questions from the highest-scoring proposition
6. **Step 6**: Generate `商业模式洞察报告.md` - Answer all questions using accessible language and analogies

## Required Resources

- **web_search**: Use to gather comprehensive information about the business topic
- **RAG_search**: Optional, for retrieving information from connected knowledge bases

## Step 1: Generate 商业模式研究报告.md

### Objective
Based on the user's research topic, explore the business model through extensive web research and produce a structured four-chapter report.

### Instructions

#### 1.1 Explore the Research Topic

**Research Workflow:**

1. **Identify key terms**: Extract key business terms from the user's topic
2. **Web search**: Use `web_search` tool extensively with multiple search queries:
   - Search in Chinese: "[话题] 商业模式", "[话题] 竞争格局", "[话题] 市占率", "[话题] 发展趋势"
   - Search in English: "[topic] business model", "[topic] competitive landscape", "[topic] market share"
   - Search for specific data: "[话题] 营收", "[话题] 净利润", "[话题] 毛利率", "[话题] 集中度"
3. **Gather comprehensive information** on:
   - Core competitiveness and its sources
   - Market share distribution among top players
   - Industry concentration trends
   - Prosperity cycle and inflection points

**Report Structure - Four Chapters:**

**第一章: 商业模式探寻 - [核心命题]**

讲明白该商业业务的核心竞争力在哪里,核心竞争力是如何形成的:
- 如果是**成本决定**的: 讲明白成本的来源,列明白成本曲线
- 如果是**大客户粘性**决定: 讲明白粘性为何强
- 如果是**渠道决定**的: 讲明白渠道如何掌控
- 如果是**技术or生态决定**的: 讲明白技术or生态壁垒为何强
- 如果是**品牌决定**的: 讲明白品牌认同的源头在哪里
- 以此类推,根据实际决定因素展开

**第二章: 商业生态探寻 - [核心命题]**

讲明白行业的前三名是如何分配市场价值的:
- 市占率、净利润占比等数据(如有)
- 前三名主要以什么形式分配市场:
  - 如果**渠道决定**市场份额: 从渠道进行拆分
  - 如果**大客户决定**市场份额: 从大客户市占率拆分
  - 如果**品牌决定**: 从价格带进行拆分
  - 如果**其他因素**: 从价值链占比进行拆分
- 中小玩家的生存现状

**第三章: 商业模式的演进方向 - [核心命题]**

讲明白集中度提升或者降低的原因:
- 从其核心决定因素分析(渠道、大客户粘性、成本、技术、品牌等)
- 集中度变化的驱动因素
- 未来演进趋势预判

**第四章: 业务景气度的演进方向 - [核心命题]**

- 如果是**趋势向好或者向差**: 讲明其向好或者向差的原因
- 如果**接近趋势拐点**: 讲明白其拐点将至或者拐点已至的逻辑

### Output Format

Save as `商业模式研究报告.md`:

```markdown
# 商业模式研究报告

## 第一章 [核心命题:商业模式探寻]

[详细内容,解释核心竞争力来源]

## 第二章 [核心命题:商业生态探寻]

[详细内容,解释市场价值分配和竞争格局]

## 第三章 [核心命题:商业模式演进方向]

[详细内容,解释集中度变化原因和趋势]

## 第四章 [核心命题:业务景气度演进方向]

[详细内容,解释景气度趋势和拐点逻辑]
```

**Important:**
- 每一章和每一个小节的章节名需要用一个**核心命题**来展现章节亮点
- 文章总标题必须为"商业模式研究报告",不得更改
- 用详实的数据和案例支撑论点

---

## Step 2: Generate 商业模式研究报告解构.md

### Objective
Deconstruct `商业模式研究报告.md` into three categories of questions based on three principles: what, why, and how.

### Instructions

Read `商业模式研究报告.md` in full, then apply the three questioning principles:

#### 2.1 Apply Questioning Principles

**原则1: 是什么(What)**
- 如果报告中解释了某个**专业术语**,则提出问题
- 询问专业术语是什么,并详细解答

**原则2: 为什么(Why)**
- 如果报告中解释了某个**逻辑关系的原因**为什么成立,则提出问题
- 或者解释了**结果为什么成立**,则提出问题
- 询问成立的原因,并详细解答

**原则3: 怎么做(How)**
- 如果报告中解释了某个**商业模式的竞争力(或壁垒)来源**,则提出问题
- 询问该竞争力(或壁垒)是如何形成的,并详细解答

#### 2.2 Structure the Deconstruction

**第一章**: "是什么"类问题
- 列出所有专业术语相关的问题
- 每个问题给出详细解答

**第二章**: "为什么"类问题
- 列出所有逻辑关系和因果关系相关的问题
- 每个问题给出详细解答

**第三章**: "怎么做"类问题
- 列出所有竞争力/壁垒形成机制相关的问题
- 每个问题给出详细解答

### Output Format

Save as `商业模式研究报告解构.md`:

```markdown
# 商业模式研究报告解构

## 第一章 是什么类问题

### [术语1]是什么?

[详细解答]

### [术语2]是什么?

[详细解答]

## 第二章 为什么类问题

### 为什么[逻辑关系/结果]成立?

[详细解答]

## 第三章 怎么做类问题

### [竞争力/壁垒]是如何形成的?

[详细解答]
```

**Important:**
- 文章总标题必须为"商业模式研究报告解构",不得更改
- 尽可能多地提取问题,确保覆盖面广

---

## Step 3: Extract Effective Propositions

### Objective
Extract as many effective propositions as possible from `商业模式研究报告.md` and fill in supporting arguments for each.

### Instructions

#### 3.1 Define "Effective Proposition"

An effective proposition must meet all three criteria:

1. **有效性(Validity)**: 该命题在第一章、第二章、第三章、第四章都有相对应的内容进行解释
2. **独立性(Independence)**: 如果不同命题探讨的对象一致,则合并该命题
3. **完备性(Completeness)**: 提出的有效命题能够概括整篇文章

#### 3.2 Extract and Structure Propositions

For each effective proposition:

1. **Identify the proposition**: What is the core claim or insight?
2. **Extract arguments**: Gather all supporting evidence from all four chapters
3. **Format as two sections**:
   - 第一节: 命题论点 (the proposition itself)
   - 第二节: 命题论据 (bullet-point arguments)

### Output Format

Save as a temporary working document (not required as final output):

```markdown
# 有效命题提取

## 命题1

### 命题论点

[命题内容]

### 命题论据

- [论据1]
- [论据2]
- [论据3]

## 命题2

### 命题论点

[命题内容]

### 命题论据

- [论据1]
- [论据2]
```

---

## Step 4: Score and Rank Propositions

### Objective
Score each effective proposition based on `商业模式研究报告解构.md` and rank them from highest to lowest.

### Instructions

#### 4.1 Scoring Algorithm

For each proposition, calculate:

**得分 = X% + Y% + Z%**

Where:
- **X%** = 该命题里的专业名词覆盖了"是什么"类问题的百分比
- **Y%** = 该命题里的逻辑覆盖了"为什么"类问题的百分比
- **Z%** = 该命题里的商业模式Knowhow覆盖了"怎么做"类问题的百分比

**Calculation Method:**
- Count how many "是什么" questions are covered by this proposition's terminology → X%
- Count how many "为什么" questions are covered by this proposition's logic → Y%
- Count how many "怎么做" questions are covered by this proposition's business knowhow → Z%
- Sum = Total score (can exceed 100%)

#### 4.2 Rank Propositions

Sort propositions by score from highest to lowest.

### Output Format

For each proposition, keep only:
- 命题本身 (the proposition)
- 命题得分 (the score)
- 打分算法 (the scoring breakdown: X% + Y% + Z%)

---

## Step 5: Generate Linked Questions

### Objective
Select the highest-scoring proposition (if the highest score is less than 200%, merge the top two propositions), and list all three categories of questions it covers from `商业模式研究报告解构.md`.

### Instructions

#### 5.1 Select Proposition(s)

- If highest score ≥ 200%: Select only the top proposition
- If highest score < 200%: Merge the top two propositions into one

#### 5.2 Extract Covered Questions

From `商业模式研究报告解构.md`, extract all questions covered by the selected proposition(s):
- All "是什么" questions covered
- All "为什么" questions covered
- All "怎么做" questions covered

### Output Format

```markdown
# 链接问题提取

## 第一章 命题本身

[命题内容,删除得分算法]

## 第二章 该命题覆盖的"是什么"类问题

- [问题1]
- [问题2]
- [问题3]

## 第三章 该命题覆盖的"为什么"类问题

- [问题1]
- [问题2]

## 第四章 该命题覆盖的"怎么做"类问题

- [问题1]
- [问题2]
```

**Important:**
- 只列出该命题覆盖的问题本身,不列出其他信息
- 第一章删除得分算法,只保留命题本身

---

## Step 6: Generate 商业模式洞察报告.md

### Objective
Based on `商业模式研究报告.md`, answer all linked questions using accessible language, analogies, and clear explanations.

### Instructions

#### 6.1 Answer Requirements

**对于"是什么"类问题:**
- 用详实的回答来解释术语本身
- 用生活中可以接触到的**通俗易懂案例进行类比**
- **各个术语的类比需套用同一套案例**,以显示互相之间的逻辑关联
- 例如: 如果解释"供应链"和"渠道壁垒",都用"开餐厅"这个案例来类比

**对于"为什么"类问题:**
- 如果答案的**重点在原因**: 重点且详细地解释原因的**必要性或者充分性**
- 如果答案的**重点在结果**: 重点且详细地解释结果的**显著性**(即为什么大概率得到这种结果)

**对于"怎么做"类问题:**
- 把核心原理用生活中可以接触到的**通俗易懂的案例进行类比解释**
- **须和"是什么"问题的类比用同一套案例**
- 在类比的过程中,用详实的回答来解释原理本身

#### 6.2 Structure the Insight Report

**第一章**: 命题本身
- 清晰陈述核心命题

**第二章**: "是什么"类问题的回答
- 每一个问题为一个小节
- 问题即为小节标题
- 用详实回答+同一套案例类比

**第三章**: "为什么"类问题的回答
- 每一个问题为一个小节
- 问题即为小节标题
- 重点解释原因必要性/充分性或结果显著性

**第四章**: "怎么做"类问题的回答
- 每一个问题为一个小节
- 问题即为小节标题
- 用同一套案例类比解释核心原理

### Output Format

Save as `商业模式洞察报告.md`:

```markdown
# 商业模式洞察报告

## 第一章 核心命题

[命题内容]

## 第二章 是什么:术语解析

### [问题1]?

[详实回答,使用生活案例类比]

### [问题2]?

[详实回答,使用同一套生活案例类比]

## 第三章 为什么:逻辑解释

### [问题1]?

[详实回答,重点解释原因必要性/充分性或结果显著性]

## 第四章 怎么做:机制剖析

### [问题1]?

[详实回答,使用与第二章同一套生活案例类比解释原理]
```

**Important:**
- 文章总标题必须为"商业模式洞察报告",不得更改
- **必须使用同一套案例**贯穿所有类比,确保逻辑连贯
- 语言要深入浅出,通俗易懂
- 用详实的回答,不要简略带过

---

## Important Reminders

1. **Sequential Execution**: Always execute steps in order (Step 1 → Step 2 → Step 3 → Step 4 → Step 5 → Step 6)
2. **Document Dependencies**: Each step depends on previous step's output
3. **Research Depth**: Step 1 must use extensive web search to gather comprehensive information
4. **Analogy Consistency**: Step 6 must use the SAME analogy framework for all "是什么" and "怎么做" questions
5. **Scoring Accuracy**: Step 4 scoring must be based on actual coverage of questions from Step 2
6. **Language Style**: Step 6 output must be accessible and use everyday analogies, not academic jargon
