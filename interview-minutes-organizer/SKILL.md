---
name: interview-minutes-organizer
description: This skill should be used when the user wants to organize and transform an interview transcript (in docx, txt, html, or markdown format) into a structured Markdown document through a five-step process. The first three steps extract text, generate verbatim transcript, and polish into formal written minutes. Step 4 extracts effective questions only. Step 5 answers each question using the respondent's voice and narrative style based on the interview transcript. Triggers include phrases like "整理访谈纪要", "整理采访记录", "interview transcript to notes", or when the user provides an interview transcript and asks for Q&A extraction.
metadata:
  author: Mike Chen
  version: '2.0'
  changelog: |
    v2.0 (2026-04-03): Step 4 now outputs questions only. Added Step 5 to answer questions using respondent's voice in narrative style.
---

# Interview Minutes Organizer

## Overview

This skill transforms an interview transcript (docx, txt, html, or markdown format) into a structured Markdown Q&A document through a five-step process. Steps 1-3 are identical to the meeting-minutes-organizer skill. Step 4 extracts effective questions only. Step 5 answers each question using the respondent's voice and narrative style.

**Supported Input Formats:**
- `.docx` - Word documents
- `.txt` - Plain text files
- `.html` - HTML files
- `.md` / `.markdown` - Markdown files

**Output Documents (in order):**
1. **Step 1**: raw.md - Structured plain text extracted from the input file
2. **Step 2**: 访谈逐字稿.md - Complete verbatim transcript with speaker identification, terminology normalization, and transcription error correction
3. **Step 3**: 访谈实录.md - Polished interview minutes with formal written language
4. **Step 4**: 有效问题.md - Extracted effective questions only (without answers)
5. **Step 5**: 访谈纪要.md - Narrative-style answers to each effective question using respondent's voice

## Required Resources

- **docx skill**: Load this skill to read and extract content from .docx transcript files

## Step 1: Extract and Convert Text

### Objective
Detect the input file format and extract its text content. Convert non-plain-text formats (docx, html) to Markdown; read plain-text formats (txt, md) directly. If the text is long, chunk it for subsequent steps.

### Instructions

1. **Detect file format** by file extension:
   - `.docx` → use the `docx` skill to extract content
   - `.html` / `.htm` → parse HTML and extract visible text, then convert to Markdown
   - `.txt` → read directly as plain text
   - `.md` / `.markdown` → read directly as Markdown (treat Markdown syntax as plain text)

2. **For .docx files**: Use the `docx` skill to extract and convert to Markdown

3. **For .html files**: Parse HTML tags, extract visible text content, and convert to Markdown format

4. **For .txt files**: Read the file directly, preserving the original line/paragraph structure

5. **For .md/.markdown files**: Read the file directly, treating Markdown syntax as plain text

6. Preserve the original paragraph/segment structure as much as possible

7. **Chunking** (if the extracted text exceeds 20,000 characters):
   - Split the text into chunks of approximately 10,000–15,000 characters each
   - Always split at a **paragraph boundary** (blank line or speaker-turn change); never break mid-sentence
   - If timestamps are present in the transcript, prefer splitting at timestamp gaps
   - Number each chunk sequentially: `[Chunk 1 of N]`, `[Chunk 2 of N]`, etc.
   - See the **Long Transcript Chunking Strategy** section below for the full processing workflow

8. Output the extracted/convered text as a Markdown document (or a series of chunk documents)

9. This document serves as input for Step 2

### Output Format
```
[Step 1 Output]
(Converted Markdown text content)
```

If chunked:
```
[Step 1 Output — Chunk 1 of N]
(Chunk 1 content)

[Step 1 Output — Chunk 2 of N]
(Chunk 2 content)

...
```

---

## Step 2: Generate 访谈逐字稿.md

### Objective
Produce a complete, unedited verbatim transcript with standardized speaker names, supplemented meeting metadata, normalized terminology, and corrected transcription errors.

### Instructions

Read the Step 1 output document in full, then perform the following:

#### 2.1 Extract and Standardize Speaker Information
- Review the entire transcript to identify all speakers
- Create a unified speaker naming convention throughout the document
- Map speaker names to their roles/titles where identifiable
- If speaker names appear inconsistently (e.g., "John", "John Smith", "JS"), standardize to one consistent form

#### 2.2 Supplement Interview Metadata
Complete the interview metadata:
- **访谈主题** (Interview Topic): Inferred from context or marked as [待确认]
- **日期/时间** (Date/Time): Extracted from transcript or marked as [待确认]
- **访谈平台** (Platform): If mentioned in transcript (e.g., Zoom, 腾讯会议, 飞书), record it

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

#### 2.5 Preserve Original Content (MANDATORY)
- **PROHIBITED**: Deleting any spoken content, even if repetitive, informal, or seemingly irrelevant
- **PROHIBITED**: Adding subjective interpretations, explanatory comments, or speculative statements
- **MANDATORY**: Retain 100% of the original transcript content

### Output Format
Save as `访谈逐字稿.md`.

---

## Step 3: Generate 访谈实录.md

### Objective
Transform the colloquial verbatim transcript into a polished written interview minutes document while preserving all substantive content.

### Instructions

Read the Step 2 output document (`访谈逐字稿.md`) in full, then perform the following:

#### 3.1 Convert Colloquial to Written Language
- Replace all colloquial expressions with their formal written equivalents
- Maintain the original meaning and tone
- **PROHIBITED**: Summarizing or condensing content
- **PROHIBITED**: Changing the declarative stance of statements
- **MANDATORY**: Preserve all original content

#### 3.2 Remove Fillers, Greetings, and Secretary Content
Delete the following types of content:

**A. Fillers and Hedging Phrases from Interviewer/Host:**
- Examples: "嗯嗯", "OK", "好的", "对对对", "然后然后", "然后的话", "就是说", "其实其实", "这个这个"
- If a sentence contains both filler and substantive content, retain only the substantive portion

**B. Greetings from Any Participant:**
- Examples: "大家好", "早上好", "下午好", "很高兴见到大家", "感谢各位参加","谢谢你的提问"
- Applies to all speakers (including interviewees/respondents)

**C. All Content from Meeting Secretary:**
- Identify the speaker role as "会议秘书" or "Secretary"
- Delete all utterances from this role entirely

**D. Transitional Courtesies from Key Speakers (e.g., CEO, Presenter):**
- Examples: "谢谢你的问题", "这个问题由我来回答", "后面那个关于XXX的问题由XXX来回答", "让我来补充一下", "我接着刚才的话题"
- Applies to speakers with roles like CEO, 主讲人, or primary presenters
- Only delete if the statement is purely transitional with no substantive content

#### 3.3 Complete Interrupted Responses
If an interviewer's follow-up question interrupts a respondent's answer:
1. First, complete the respondent's answer to the previous question
2. Then, present the interviewer's follow-up question separately
3. Follow with the respondent's answer to the follow-up question
- This ensures logical completeness of each Q&A pair

#### 3.4 Preserve Core Content (MANDATORY)
- **PROHIBITED**: Reordering sentences outside the interrupt-completion scenario described in 3.3
- **PROHIBITED**: Deleting substantive statements (only fillers per 3.2 are removable)
- **MANDATORY**: Maintain the logical flow and completeness of the interview

### Output Format
Save as `访谈实录.md`.

---

## Step 4: Generate 有效问题.md

### Objective
Based on `访谈实录.md`, extract effective questions that meet three criteria. This step outputs questions only, without answers.

### Instructions

Read the Step 3 output document (`访谈实录.md`) in full, then perform the following:

#### 4.1 Define "Effective Question"

An effective question must meet all three criteria:

1. **有效性 (Validity)**: The respondent provided clear arguments and complete supporting evidence when answering.
   - Must contain at least one explicit argument/claim
   - Must contain supporting evidence (data, examples, reasoning, or detailed explanation)
   - Vague, evasive, or incomplete answers do not qualify

2. **独立性 (Independence)**: If a question is logically connected to the previous question, merge them into a single question.
   - Follow-up questions that extend the same topic should be merged
   - Questions on completely different topics should remain separate
   - The merged question should capture the full logical scope

3. **完备性 (Completeness)**: All extracted "effective questions" together must cover the entire interview content.
   - No substantive content from the interview should be left out
   - Every major topic discussed must be represented in at least one question

#### 4.2 Extract and Merge Questions

Process the interview transcript systematically:

1. **Identify all questions** asked by the interviewer
2. **Evaluate each answer** against the validity criteria (arguments + evidence)
3. **Merge related questions** that share logical connections:
   - Look for follow-up questions ("接着刚才的问题", "那关于这一点", "除此之外")
   - Look for questions on the same sub-topic
   - Merge into a single comprehensive question that captures the full scope
4. **Ensure completeness** by verifying all substantive content is covered

#### 4.3 Format Output

Each question must follow this format:

```markdown
### 问题 N

**访谈者**: [合并后的完整问题，包含所有相关子问题]
```

Where:
- N is the sequential question number (1, 2, 3...)
- The question starts with "访谈者:" followed by the merged question text
- Questions should be polished to formal written language
- Do NOT include answers in this step

#### 4.4 Quality Checks

Before finalizing, verify:

1. **Validity check**: Each question has a corresponding answer in the interview with clear arguments and supporting evidence
2. **Independence check**: No two questions overlap in logical scope; related questions are properly merged
3. **Completeness check**: All major topics from the interview are covered; no substantive content is omitted
4. **Accuracy check**: Questions faithfully represent the original interview content without distortion

### Output Format
Save as `有效问题.md` with the following structure:

```markdown
# 访谈有效问题提取

> **访谈主题**: [主题]
> **访谈日期**: [日期]
> **问题总数**: [N]

---

### 问题 1

**访谈者**: [问题内容]

---

### 问题 2

**访谈者**: [问题内容]

---

[继续至问题 N]
```

---

## Step 5: Generate 访谈纪要.md

### Objective
Based on `访谈实录.md` and `有效问题.md`, answer each effective question using the respondent's voice and narrative style. The output is a comprehensive interview minutes document.

### Instructions

Read both `访谈实录.md` and `有效问题.md` in full, then perform the following for each question:

#### 5.1 Answer Comprehensively

For each question in `有效问题.md`:

1. **Read the entire transcript**: Thoroughly review `访谈实录.md` to identify ALL content related to this question
2. **Collect all arguments**: Gather every argument/claim the respondent made that relates to this question, regardless of where it appears in the transcript
3. **Collect all evidence**: Gather all supporting evidence (data, examples, reasoning, detailed explanations) for each argument
4. **Include everything**: Do not omit any relevant content, even if it was mentioned briefly or in passing

#### 5.2 Use Narrative Style

**MANDATORY requirements:**

1. **Narrative prose, NOT bullet points**: Write in flowing, narrative paragraphs. Do NOT use bullet points, numbered lists, or summary-style formatting.
   - ❌ WRONG: "主要有三个方面：第一...第二...第三..."
   - ✅ CORRECT: "关于这个问题，目前的情况是...具体而言...此外..."

2. **Start with arguments, then expand with evidence**: 
   - First, clearly state the main argument/position
   - Then, elaborate with supporting evidence (data, examples, reasoning)
   - Structure: 论点 → 论据展开

3. **Strictly follow the transcript**: 
   - All arguments and evidence must come directly from `访谈实录.md`
   - **PROHIBITED**: Adding speculative statements, assumptions, or interpretations
   - **PROHIBITED**: Making inferences beyond what the respondent actually said
   - **MANDATORY**: Use the respondent's actual statements, rephrased into formal written language

#### 5.3 Format Output

Each Q&A must follow this format:

```markdown
### 问题 N

**访谈者**: [问题原文，从有效问题.md复制]

**[受访者称呼]**: [叙述式回答，以受访者口吻]
```

Where:
- The question is copied verbatim from `有效问题.md`
- The answer starts with the respondent's name/role (e.g., "**李总**:" or "**受访者**:")
- The answer is written in narrative prose style
- The answer uses the respondent's voice and perspective (first-person or reported speech)

#### 5.4 Quality Checks

Before finalizing, verify:

1. **Completeness**: Each answer includes ALL relevant arguments and evidence from the transcript
2. **Narrative style**: Answers are written in flowing prose, NOT bullet points or lists
3. **Accuracy**: No speculative or inferred content; all statements trace back to the transcript
4. **Argument-first structure**: Each answer clearly presents arguments first, then supporting evidence
5. **Respondent's voice**: Answers sound like the respondent is speaking, not a third-party summary

### Output Format
Save as `访谈纪要.md` with the following structure:

```markdown
# 访谈纪要

> **访谈主题**: [主题]
> **访谈日期**: [日期]
> **受访者**: [受访者称呼]
> **问题总数**: [N]

---

### 问题 1

**访谈者**: [问题原文]

**[受访者称呼]**: [叙述式回答，先给出论点，再用论据展开，使用叙述式文字]

---

### 问题 2

**访谈者**: [问题原文]

**[受访者称呼]**: [叙述式回答，先给出论点，再用论据展开，使用叙述式文字]

---

[继续至问题 N]

---

**免责声明**: 本文件仅为访谈内容整理，不构成任何专业建议。所有内容均基于访谈实录，未添加推测性表述。
```

---

## Long Transcript Chunking Strategy

This section applies **only** when Step 1 detects that the extracted text exceeds 30,000 characters. For shorter transcripts, ignore this section entirely and process the text as a single unit.

### Trigger

After Step 1 extracts the full text, count the total characters. If the count exceeds **20,000 characters**, activate chunking.

### Chunking Rules (performed in Step 1)

1. Target chunk size: **10,000–15,000 characters** per chunk
2. Split at **paragraph boundaries** (blank lines or speaker-turn changes); never break mid-sentence
3. If timestamps exist in the transcript, prefer splitting at timestamp gaps
4. Number chunks sequentially: `[Chunk 1 of N]`, `[Chunk 2 of N]`, etc.

### Execution Flow

Process **all chunks through one step** before advancing to the next step. This ensures global consistency of speaker names and terminology across the entire document.

```
Step 1: Extract full text → Chunk into N parts → Output chunk list
         ↓
Step 2: Process Chunk 1 → Build speaker map + terminology dictionary
         Process Chunk 2 → Carry forward speaker map + terminology dictionary + context summary
         ... (repeat for all N chunks)
         → After all chunks: finalize global speaker map + terminology dictionary
         ↓
Step 3: Process Chunk 1 (using finalized speaker map + terminology dictionary)
         Process Chunk 2 → Carry forward context summary
         ... (repeat for all N chunks)
         ↓
Step 4: Process Chunk 1 → Extract questions
         Process Chunk 2 → Carry forward context summary + merge questions
         ... (repeat for all N chunks)
         → After all chunks: finalize question list
         ↓
Step 5: Process Chunk 1 → Answer questions
         Process Chunk 2 → Carry forward context summary
         ... (repeat for all N chunks)
         ↓
Final:  Concatenate all chunk outputs in order → Produce final document
```

Key points:
- Complete **all chunks within one step** before moving to the next step (all Step 2 first, then all Step 3, then all Step 4)
- This ensures the speaker map and terminology dictionary are fully built in Step 2 before Step 3 begins polishing language
- The final document is a simple ordered concatenation of all chunk outputs; interview metadata (header, speaker list) appears only once at the top

### Context Carry-Forward (replaces overlap)

Instead of overlapping content between chunks, use a **context summary** to maintain continuity:

1. After finishing each chunk (through Step 4), produce a brief **context summary** (≤ 500 characters) containing:
   - The current topic being discussed
   - Any unfinished argument or thread
   - The last speaker and their stance
   - Any newly introduced terminology or abbreviations

2. When processing the next chunk, prepend this context summary as background information

3. Additionally, carry forward two persistent artifacts across all chunks:
   - **Speaker Map**: `Speaker Name → Role/Title` (append new speakers as they appear)
   - **Terminology Dictionary**: `Original Term → Standardized Form` (append new terms as they appear)

### Example Context Summary

```
[Context from Chunk 1]
Topic: Discussion of Q3 revenue decline in the retail segment.
Last speaker: Zhang Wei (CFO), explaining that the decline was primarily driven by
reduced foot traffic in tier-3 cities.
Unfinished thread: Zhang Wei was about to address the margin impact when the chunk ended.
New terms: "新零售" → "新零售业务板块", "三线" → "三线城市"
```

---

## Important Reminders

1. **Sequential Execution**: Always execute steps in order (Step 1 → Step 2 → Step 3 → Step 4 → Step 5)
2. **Document Preservation**: Each step's output becomes the input for the next step
3. **Content Integrity**: Never delete substantive content; only remove fillers per the rules
4. **Terminology Consistency**: Maintain consistent use of terminology throughout all steps
5. **Question Quality**: Step 4 must ensure all extracted questions meet the three criteria: validity (有效性), independence (独立性), and completeness (完备性)
6. **Question Merging**: Related questions must be merged; do not output redundant or overlapping questions
7. **Step 5 Narrative Style**: Step 5 answers must be in narrative prose style (叙述式), NOT bullet points or lists
8. **Step 5 Completeness**: Each answer in Step 5 must include ALL relevant arguments and evidence from the entire transcript
9. **Step 5 Accuracy**: No speculative content in Step 5; all statements must trace back to the interview transcript
