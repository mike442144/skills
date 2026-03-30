---
name: meeting-minutes-organizer
description: This skill should be used when the user wants to organize and transform a meeting transcript (in docx, txt, html, or markdown format) into a structured, objective, and concise Markdown meeting notes document. Triggers include phrases like "整理会议纪要", "整理访谈记录", "transcript to notes", or when the user provides a transcript file (.docx/.txt/.html/.md) and asks for it to be organized. The skill processes the transcript in three sequential steps, each producing an intermediate document.
metadata:
  author: Mike Chen
  version: '1.6'
---

# Meeting Minutes Organizer

## Overview

This skill transforms a meeting transcript (docx, txt, html, or markdown format) into a structured Markdown meeting notes document through a four-step process. Each step produces an intermediate document that serves as input for the next step.

**Supported Input Formats:**
- `.docx` - Word documents
- `.txt` - Plain text files
- `.html` - HTML files
- `.md` / `.markdown` - Markdown files

**Output Documents (in order):**
1. **Step 1**: Structured plain text extracted from the input file
2. **Step 2**: 《访谈逐字稿》- Complete verbatim transcript with speaker identification, terminology normalization, and transcription error correction
3. **Step 3**: 《会议实录》- Polished meeting minutes with formal written language
4. **Step 4**: 《内容梳理》- Restructured Q&A sections with split multiple questions

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

7. **Chunking** (if the extracted text exceeds 30,000 characters):
   - Split the text into chunks of approximately 15,000–20,000 characters each
   - Always split at a **paragraph boundary** (blank line or speaker-turn change); never break mid-sentence
   - If timestamps are present in the transcript, prefer splitting at timestamp gaps
   - Number each chunk sequentially: `[Chunk 1 of N]`, `[Chunk 2 of N]`, etc.
   - See the **Long Transcript Chunking Strategy** section below for the full processing workflow

8. Output the extracted/converted text as a Markdown document (or a series of chunk documents)

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

## Step 2: Generate 《访谈逐字稿》

### Objective
Produce a complete, unedited verbatim transcript with standardized speaker names, supplemented meeting metadata, normalized terminology, and corrected transcription errors.

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

#### 2.5 Preserve Original Content (MANDATORY)
- **PROHIBITED**: Deleting any spoken content, even if repetitive, informal, or seemingly irrelevant
- **PROHIBITED**: Adding subjective interpretations, explanatory comments, or speculative statements
- **MANDATORY**: Retain 100% of the original transcript content

### Output Format
Follow the 《访谈逐字稿》 section in `assets/meeting-template.md`.

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

## Step 4: Generate 《内容梳理》

### Objective
Restructure Q&A sections for improved clarity by splitting multiple questions into individual Q&A pairs while preserving all content.

### Instructions

Read the Step 3 output document in full, then perform the following:

#### 4.1 Restructure Multiple-Question Q&A Pairs
If a questioner asks multiple questions in a single utterance during a Q&A section:
- Split the questions into individual Q&A pairs
- Each question should be paired with its corresponding answer
- Maintain the original sequence of questions and answers
- Do not alter the content of any question or answer
- **Use `---` to separate each Q&A pair** for improved readability
- Example transformation:
  - Before: "提问者: 问题一？问题二？问题三？ 回答者1: 回答一、二。回答者2: 回答三。"
  - After:
    ```
    提问者: 问题一？ 回答者1: 回答一。
    ---
    提问者: 问题二？ 回答者1: 回答二。
    ---
    提问者: 问题三？ 回答者2: 回答三。
    ```

#### 4.2 Preserve Content Integrity (MANDATORY)
- **PROHIBITED**: Altering, summarizing, or condensing any substantive content
- **PROHIBITED**: Changing the wording of questions or answers
- **MANDATORY**: Retain 100% of the original meeting content
- **MANDATORY**: Only restructure the format/organization, not the substance

### Output Format
Follow the 《内容梳理》 section in `assets/meeting-template.md`.

---

## Long Transcript Chunking Strategy

This section applies **only** when Step 1 detects that the extracted text exceeds 30,000 characters. For shorter transcripts, ignore this section entirely and process the text as a single unit.

### Trigger

After Step 1 extracts the full text, count the total characters. If the count exceeds **30,000 characters**, activate chunking.

### Chunking Rules (performed in Step 1)

1. Target chunk size: **15,000–20,000 characters** per chunk
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
Step 4: Process Chunk 1
         Process Chunk 2 → Carry forward context summary
         ... (repeat for all N chunks)
         ↓
Final:  Concatenate all chunk outputs in order → Produce final document
```

Key points:
- Complete **all chunks within one step** before moving to the next step (all Step 2 first, then all Step 3, then all Step 4)
- This ensures the speaker map and terminology dictionary are fully built in Step 2 before Step 3 begins polishing language
- The final document is a simple ordered concatenation of all chunk outputs; meeting metadata (header, speaker list) appears only once at the top

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

1. **Sequential Execution**: Always execute steps in order (Step 1 → Step 2 → Step 3 → Step 4)
2. **Document Preservation**: Each step's output becomes the input for the next step
3. **Content Integrity**: Never delete substantive content; only remove fillers per the rules
4. **Terminology Consistency**: Maintain consistent use of terminology throughout all steps
5. **Q&A Focus**: Pay special attention to preserving Q&A sections accurately and split multiple questions into individual Q&A pairs per section 4.1
