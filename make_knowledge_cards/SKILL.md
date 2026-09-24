---
name: make-knowledge-cards
description: Extracts key knowledge from text/markdown/PDF/PPT/DOCX files and generates 5-10 concise knowledge cards. Each card covers one concept with title, core knowledge, explanation, example, self-test question, and deeper thinking. Invoke when user provides a document and wants it condensed into study cards.
license: MIT
---

# Make Knowledge Cards

## Overview

This skill transforms any document (TXT, Markdown, PDF, PPTX, DOCX) into a set of 5-10
concise knowledge cards. Each card isolates a single knowledge point with structured fields
for efficient learning and review.

## Workflow

Follow these steps in order:

### Step 1: Extract Content

Run the extraction script to get raw text from the source file.

```bash
python scripts/extract_content.py <input_file> [--output <output_path>]
```

- Supports: `.txt`, `.md`, `.pdf`, `.pptx`, `.docx`
- If `--output` is omitted, text is printed to stdout.
- The script auto-detects format by file extension.

If the script is unavailable or the file format is unsupported, read the file directly
with the Read tool (for text-based formats) or ask the user to provide the text.

### Step 2: Analyze and Identify Knowledge Points

Read the extracted text and identify 5-10 truly important knowledge points.

**Selection criteria (must follow ALL):**

1. **Importance**: Choose concepts that are central to the document's purpose, not
   peripheral details or transitions.
2. **One point per card**: Each card must focus on exactly one knowledge point. If a
   concept has multiple sub-points that can stand alone, split them into separate cards.
3. **No duplication**: If the same concept appears in multiple sections, consolidate it
   into a single card. Do not repeat content across cards.
4. **No fabrication**: Every piece of information in a card must come from the source
   document. Do not invent examples, explanations, or facts not present in the text.
5. **Quality over quantity**: If the document only contains 3 substantive knowledge
   points, produce 3 cards. Do not pad with low-value content to reach 5 cards.
   Produce fewer cards rather than diluting quality.
6. **Coverage**: Aim to cover the full scope of the document's key ideas, not just the
   first few sections.

### Step 3: Build Card Data

For each knowledge point, create a card object with these exact fields:

| Field | Purpose | Guidelines |
|-------|---------|------------|
| `title` | Short title (5-15 words) | Name the knowledge point clearly. Use a phrase, not a full sentence. |
| `core_knowledge` | The core fact/concept | State the key knowledge concisely. This is the "what" — the essential takeaway. 2-4 sentences. |
| `explanation` | Brief explanation | Explain *why* or *how* it works. Clarify the concept so a learner can understand it. 2-4 sentences. Must come from the document. |
| `example` | Concrete example from the text | If the document provides an example, use it. If no example exists in the source, write "原文未提供示例" (no example provided in source). Never fabricate examples. |
| `self_test` | Self-test question | Write a question that tests understanding of this knowledge point. The answer should be derivable from the card content. Frame it as a question, not a task. |
| `summary` | Summary or deeper thinking | Either summarize the key takeaway in 1-2 sentences, or raise a deeper question/insight that extends the concept. Choose whichever adds more value for this specific knowledge point. |

**Card data format (JSON):**

```json
{
  "source_file": "document.pdf",
  "source_title": "Document Title (extracted from content or filename)",
  "card_count": 6,
  "cards": [
    {
      "title": "Card Title",
      "core_knowledge": "Core knowledge content.",
      "explanation": "Brief explanation.",
      "example": "Concrete example from the text.",
      "self_test": "Self-test question?",
      "summary": "Summary or deeper thinking."
    }
  ]
}
```

### Step 4: Generate HTML Output

Write the card data as a JSON file, then run the generation script:

```bash
python scripts/generate_cards.py <cards_json> [--output <output_html>]
```

- The script produces a self-contained HTML file with styled cards.
- If `--output` is omitted, the HTML is printed to stdout.
- Default output filename: `knowledge_cards.html`

### Step 5: Deliver

Save the HTML file and share it with the user. Mention:
- The source file name.
- The number of cards generated.
- If fewer than 5 cards were produced, explain that the source material did not contain
  enough distinct knowledge points and list how many were found.

## Content Extraction Details

The `extract_content.py` script handles each format as follows:

| Format | Library | Strategy |
|--------|---------|----------|
| `.txt` | built-in | Read file directly, decode as UTF-8 |
| `.md` | built-in | Read file directly, preserve markdown structure |
| `.pdf` | `pypdf` | Extract text per page, concatenate |
| `.pptx` | `python-pptx` | Extract text from each slide (title, body, notes) |
| `.docx` | `python-docx` | Extract text from paragraphs and tables |

If a library is missing, the script reports which package to install.

## Quality Checklist

Before delivering, verify each card:

- [ ] Title is concise and descriptive (5-15 words)
- [ ] Core knowledge comes directly from the source document
- [ ] Explanation clarifies the concept without adding new information
- [ ] Example is from the source, or explicitly marked as absent
- [ ] Self-test question is answerable from the card content
- [ ] Summary adds value (not a restatement of core knowledge)
- [ ] No two cards cover the same knowledge point
- [ ] Total card count is 5-10 (or fewer if the source is limited)
- [ ] No fabricated content anywhere in any card

## Error Handling

- **Empty extraction**: If the source file yields no text, inform the user and stop.
- **Corrupt file**: Report the error and suggest trying a different format.
- **Very short document**: If the text is under 200 words, produce as many cards as
  the content supports (even if just 1-2) and explain the limitation.
- **Non-text PDF**: If a PDF is image-based and yields no text, inform the user that
  OCR is needed and the current skill does not support it.
