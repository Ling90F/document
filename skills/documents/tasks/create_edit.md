# Task: Create or edit a DOCX

Use this guide for CREATE and EDIT authoring tasks. Follow the mode, runtime, template, Google Docs, and render requirements in `SKILL.md`.

Before writing or rewriting substantive content, read:

`references/deliverable_content.md`

## 1. Define the authoring scope

### CREATE

Determine the real purpose, audience, required content, and delivery format. Use the applicable template or design preset.

### EDIT

Read the source and the requested change scope before editing.

- Preserve the established structure, terminology, voice, and design.
- Prefer local replacements to broad rewrites.
- Do not change compliant content outside the requested scope.
- Use comments or tracked changes only when requested or required by the task.

## 2. Authoring tool

Use `python-docx` for ordinary document construction and edits:

- paragraphs and runs;
- paragraph styles;
- tables and cell content;
- headers and footers;
- margins, sections, and page setup.

Use bundled OOXML helpers when `python-docx` does not support the required feature, especially for tracked changes, comments, fields, relationships, or deterministic low-level edits.

For Google Docs-targeted output, build the title as a plain paragraph with explicit formatting. Do not use Word's built-in `Title` style. Run `scripts/google_docs_title_sanitize.py` before render/import.

## 3. Common implementation details

### Header/footer tables require an explicit width

```python
from docx.shared import Inches
from docx.enum.text import WD_ALIGN_PARAGRAPH

section = doc.sections[0]
footer = section.footer
table = footer.add_table(rows=1, cols=3, width=Inches(6.5))
table.rows[0].cells[0].paragraphs[0].alignment = WD_ALIGN_PARAGRAPH.LEFT
```

### Set Word font mappings when required

Some renderers need both `run.font.name` and `w:rFonts`:

```python
from docx.oxml.ns import qn

run.font.name = "Gill Sans"
run._element.rPr.rFonts.set(qn("w:ascii"), "Gill Sans")
run._element.rPr.rFonts.set(qn("w:hAnsi"), "Gill Sans")
```

### Replacing header content

Do not assume an existing header paragraph has a reliable `clear()` method. Remove runs or replace the paragraph XML when needed.

## 4. Content gate

Before render QA, run the content audit and compression pass in `references/deliverable_content.md`.

The final text must:

- contain only material that belongs in the standalone deliverable;
- use no more explanation than the reader needs;
- state concrete mechanics, conditions, parameters, and measured results instead of inferred benefits;
- avoid sentences that bundle conclusion, reason, benefit, attitude, and recommendation;
- remove repeated framing, rationale, summaries, and unsupported evaluative wording;
- preserve unknown facts as placeholders or document-native uncertainty rather than inventing them.

Do not proceed to visual QA until the content passes.

## 5. Render and verify

After each meaningful authoring batch, follow `tasks/verify_render.md`.

For final delivery:

1. finish the content pass;
2. render the DOCX to page PNGs;
3. inspect every page at 100% zoom;
4. fix layout defects;
5. re-render after layout-sensitive or OOXML changes;
6. deliver only after the latest render is clean.

If LibreOffice/`soffice` is unavailable, use the structural fallback in `SKILL.md` and disclose that visual QA was not completed.

## 6. Output hygiene

Keep final deliverables separate from temporary builders, page renders, and debug artifacts.

Put change summaries, implementation explanations, QA status, and next-step suggestions in the chat response, not in the document, unless the requested artifact explicitly calls for them.
