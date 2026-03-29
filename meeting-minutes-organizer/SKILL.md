---
name: meeting-minutes-organizer
description: This skill should be used when the user wants to organize and transform a meeting transcript (in docx, txt, html, or markdown format) into a structured, objective, and concise Markdown meeting notes document. Triggers include phrases like "整理会议纪要", "整理访谈记录", "transcript to notes", or when the user provides a transcript file (.docx/.txt/.html/.md) and asks for it to be organized. The skill processes the transcript in three sequential steps, each producing an intermediate document.
metadata:
  author: Mike Chen
  version: '1.1'
---

# Meeting Minutes Organizer

## Overview

This skill transforms a meeting transcript (docx, txt, html, or markdown format) into a structured Markdown meeting notes document through a three-step process. Each step produces an intermediate document that serves as input for the next step.

**Supported Input Formats:**
- `.docx` - Word documents
- `.txt` - Plain text files
- `.html` - HTML files
- `.md` / `.markdown` - Markdown files

**Output Documents (in order):**
1. **Step 1**: Structured plain text extracted from the input file
2. **Step 2**: 《访谈逐字稿》- Complete verbatim transcript with speaker identification, terminology normalization, and Q&A restructuring
3. **Step 3**: 《会议实录》- Polished meeting minutes with formal written language

## Required Resources

- **docx skill**: Load this skill to read and extract content from .docx transcript files

## Step 1: Extract Text from Input File

### Objective
Extract all text content from the user's transcript file (docx, txt, html, or markdown) and output it as structured plain text.

### Instructions

1. **For .docx files**: Use the `docx` skill to load and read the file
2. **For .txt files**: Read the file directly, preserving the original line/paragraph structure
3. **For .html files**: Parse the HTML and extract visible text content, ignoring HTML tags
4. **For .md/.markdown files**: Read the file directly, treating Markdown syntax as plain text
5. Preserve the original paragraph/segment structure as much as possible
6. Output the extracted text as a plain text document
7. This document serves as input for Step 2

### Output Format
```
[Step 1 输出]
（原始文本内容，按原文档段落顺序排列）
```

---

## Step 2: Generate 《访谈逐字稿》

### Objective
Produce a complete, unedited verbatim transcript with standardized speaker names, supplemented meeting metadata, normalized terminology, and restructured Q&A sections.

### Instructions

Read the Step 1 output document in full, then perform the following:

#### 2.1 Extract and Standardize Speaker Information
- Review the entire transcript to identify all speakers
- Create a unified speaker naming convention throughout the document
- Map speaker names to their roles/titles where identifiable
- If speaker names appear inconsistently (e.g., "John", "John Smith", "JS"), standardize to one consistent form

#### 2.2 Supplement Meeting Metadata
Using the template in `assets/meeting-template.md`, complete the meeting metadata:
- **会议主题** (Meeting Topic): Inferred from context or marked as [待确认]
- **日期/时间** (Date/Time): Extracted from transcript or marked as [待确认]
- **参会人** (Participants): List all identified speakers with their roles
- **会议平台** (Platform): If mentioned in transcript (e.g., Zoom, 腾讯会议, 飞书), record it

#### 2.3 Normalize Terminology
Based on context (industry, company business, mentioned products/technologies):
- Restore colloquialisms, slang, and abbreviations to their standard forms
- Examples: "某大" → "某大型银行", "DPO" → "数据保护官", product code names to full product names
- Use full official names for listed companies, products, and technical roadmaps when identifiable from context
- If uncertain about a specific term's meaning, preserve the original wording

#### 2.4 Correct Transcription Errors
- Review for obvious speech-to-text errors (homophones, character recognition mistakes)
- Apply conservative corrections only when the intended meaning is clear from context
- If uncertain about a correction, leave the original text unchanged
- Document any corrections made for transparency

#### 2.5 Restructure Multiple-Question Q&A Pairs
If a questioner asks multiple questions in a single utterance during a Q&A section:
- Split the questions into individual Q&A pairs
- Each question should be paired with its corresponding answer
- Maintain the original sequence of questions and answers
- Do not alter the content of any question or answer
- Example transformation:
  - Before: "Q: 问题一？问题二？问题三？ A: 回答一、二、三。"
  - After:
    - "Q: 问题一？ A: 回答一、二、三。"
    - "Q: 问题二？ A: 回答一、二、三。"
    - "Q: 问题三？ A: 回答一、二、三。"

#### 2.6 Preserve Original Content (MANDATORY)
- **PROHIBITED**: Deleting any spoken content, even if repetitive, informal, or seemingly irrelevant
- **PROHIBITED**: Adding subjective interpretations, explanatory comments, or speculative statements
- **MANDATORY**: Retain 100% of the original transcript content

### Output Format
Follow the structure defined in `assets/meeting-template.md` for this step:

```
## 会议基本信息

| 字段 | 内容 |
|------|------|
| 会议主题 | （填写内容） |
| 日期/时间 | （填写内容） |
| 参会人 | （填写内容） |
| 会议平台 | （填写内容） |

---

## 访谈逐字稿

> **说明**：以下为《访谈逐字稿》，完整保留原始发言内容，未经删减。

### 发言人列表

| 发言人 | 身份/角色 |
|--------|-----------|
| （姓名） | （角色） |

### 正文

（完整的逐字稿内容，使用统一后的发言人名称）
```

---

## Step 3: Generate 《会议实录》

### Objective
Transform the colloquial verbatim transcript into a polished written meeting minutes document while preserving all substantive content.

### Instructions

Read the Step 2 output document in full, then perform the following:

#### 3.1 Convert Colloquial to Written Language
- Replace all colloquial expressions with their formal written equivalents
- Maintain the original meaning and tone
- **PROHIBITED**: Summarizing or condensing content
- **PROHIBITED**: Changing the declarative stance of statements
- **MANDATORY**: Preserve all original content

#### 3.2 Remove Fillers and Hedging Phrases
Delete sentences that are purely acknowledgments or verbal fillers from the interviewer/host:
- Examples: "嗯嗯", "OK", "好的", "对对对", "然后然后", "然后的话", "就是说", "其实其实", "这个这个"
- If a sentence contains both filler and substantive content, retain only the substantive portion
- This rule applies ONLY to the interviewer's/host's statements; do not remove fillers from the interviewee's/participants' responses

#### 3.3 Complete Interrupted Responses
If an interviewer's follow-up question interrupts a respondent's answer:
1. First, complete the respondent's answer to the previous question
2. Then, present the interviewer's follow-up question separately
3. Follow with the respondent's answer to the follow-up question
- This ensures logical completeness of each Q&A pair

#### 3.4 Preserve Core Content (MANDATORY)
- **PROHIBITED**: Reordering sentences outside the interrupt-completion scenario described in 3.3
- **PROHIBITED**: Deleting substantive statements (only fillers per 3.2 are removable)
- **MANDATORY**: Maintain the logical flow and completeness of the meeting

### Output Format
Follow the structure defined in `assets/meeting-template.md` for this step:

```
## 会议实录

> **说明**：以下为《会议实录》，已将口语化表述转为书面语，删除了无实质内容的应和语句和口头禅。

### 发言人列表

| 发言人 | 身份/角色 |
|--------|-----------|
| （同上） | （同上） |

### 正文

（经过处理的会议实录内容）
```

---

## Important Reminders

1. **Sequential Execution**: Always execute steps in order (Step 1 → Step 2 → Step 3)
2. **Document Preservation**: Each step's output becomes the input for the next step
3. **Content Integrity**: Never delete substantive content; only remove fillers per the rules
4. **Terminology Consistency**: Maintain consistent use of terminology throughout all steps
5. **Q&A Focus**: Pay special attention to preserving Q&A sections accurately and split multiple questions into individual Q&A pairs per section 2.5
