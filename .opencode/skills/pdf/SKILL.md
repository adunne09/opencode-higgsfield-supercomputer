---
name: pdf
description: |
  Generate styled PDF documents from markdown text, reports, or structured data.
  Use when user asks to create, export, or save content as a PDF file.
  Handles headings, paragraphs, bold/italic, lists, tables, code blocks, and horizontal rules.
---
# PDF Generation Skill

Convert markdown content to a branded, styled PDF. Upload it and return the CDN URL as a file artifact.

## Dependencies

Install once per session if not already available:

```bash
pip install reportlab
```

## Workflow

1. Collect content -- gather the full text from the conversation or ask the user to provide it.

2. Write markdown to a file inside the project:
   ./assets/<content-slug>.md

3. Write the generator script to ./gen_pdf.py using the template below.

4. Generate the PDF:
   ```bash
   python3 ./gen_pdf.py ./assets/<slug>.md ./generations/<slug>.pdf "<Document Title>"
   ```

5. Upload:
   higgsfield_upload(files=["./generations/<slug>.pdf"])

6. Return the CDN URL as a file artifact:
   {"type": "file", "id": "<cdn_url>", "preview": "<cdn_url>", "label": "<Title>.pdf"}

## Generator Script -- write to ./gen_pdf.py

The script uses reportlab with a built-in markdown parser to produce a styled PDF.
It supports: H1/H2/H3 headings, bold/italic, inline code, fenced code blocks, bullet lists,
numbered lists, GFM pipe tables, horizontal rules, and regular paragraphs.

Brand palette (matches Higgsfield UI tokens):
- BRAND: #4FCEE4 (title color, header rule)
- DARK: #1A1C1F (body text)
- MUTED: #898A8B (table borders, HR)
- LIGHT: #F0F0F0 (code blocks, table header bg, alternating rows)

Key functions:
- _styles() -- builds all ParagraphStyle objects
- _inline(text) -- converts inline markdown to ReportLab XML (bold, italic, inline code)
- _parse_table(lines, styles) -- parses GFM table block into a ReportLab Table
- _build_story(md_text, title, styles) -- walks through markdown lines, emits flowables
- build_pdf(md_path, pdf_path, title) -- reads markdown file, calls _build_story, builds PDF

Usage:
```bash
python3 gen_pdf.py input.md output.pdf "Document Title"
```

## Naming Convention

| Item | Pattern | Example |
|---|---|---|
| Markdown source | ./assets/<slug>.md | ./assets/skincare-report.md |
| PDF output | ./generations/<slug>.pdf | ./generations/skincare-report.pdf |
| Artifact label | Human title + .pdf | Skincare Trend Report.pdf |

## Supported Markdown

| Element | Syntax |
|---|---|
| Headings | # H1, ## H2, ### H3 |
| Bold | **text** or __text__ |
| Italic | *text* or _text_ |
| Inline code | backtick code backtick |
| Fenced code block | triple-backtick ... triple-backtick |
| Bullet list | - item / * item |
| Numbered list | 1. item |
| Table | GFM pipe table with --- separator |
| Horizontal rule | --- |
| Paragraph | Any non-special block of text |

## Error Handling

If reportlab is missing: run pip install reportlab first.

If generation fails with a parse error: simplify the markdown (remove unsupported HTML tags,
nested lists, or images) and retry.

If the PDF is blank: check that ./assets/<slug>.md was written correctly before running the script.
