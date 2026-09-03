# Task: Create / edit a DOCX

Use this guide for CREATE and EDIT authoring tasks.

Before writing substantive artifact content, read and follow:

`references/deliverable_content.md`

The document must read as a standalone real-world deliverable, not as a transcript of the assistant-user conversation or a record of the production process.

## 1. Authoring mode requirements

### CREATE

For a new document:

- determine the real document purpose and audience;
- select/follow the applicable template or design preset;
- write only content that belongs in the finished artifact;
- do not add assistant-style framing, work summaries, generic introductions, generic conclusions, QA notes, tool details, or next-step suggestions unless the requested document type genuinely requires them;
- do not invent missing facts merely to make the document appear complete.

### EDIT

For an existing document:

- preserve the established voice, structure, terminology, and design unless broader change is explicitly requested;
- prefer minimal local edits over rewriting entire sections;
- apply `references/deliverable_content.md` primarily to newly inserted or rewritten text;
- do not rewrite compliant existing prose merely to impose a generic style;
- do not expand a narrow edit request into a broader rewrite.

## 2. Default tool: python-docx

Use `python-docx` for:

- paragraphs and runs;
- Word paragraph styles;
- tables and cell text;
- headers/footers;
- margins and page setup;
- ordinary document structure.

For Google Docs-targeted output, do not use Word's built-in `Title` paragraph style. Build the title as a plain paragraph with explicit formatting, then run `scripts/google_docs_title_sanitize.py` before render/import.

## 3. Practical python-docx gotchas

### Header/footer tables require a width

```python
from docx.shared import Inches
from docx.enum.text import WD_ALIGN_PARAGRAPH

section = doc.sections[0]
footer = section.footer
table = footer.add_table(rows=1, cols=3, width=Inches(6.5))
table.rows[0].cells[0].paragraphs[0].alignment = WD_ALIGN_PARAGRAPH.LEFT
```

### Fonts can require both `run.font.name` and `w:rFonts`

```python
from docx.oxml.ns import qn

run.font.name = "Gill Sans"
run._element.rPr.rFonts.set(qn("w:ascii"), "Gill Sans")
run._element.rPr.rFonts.set(qn("w:hAnsi"), "Gill Sans")
```

### Replacing header content

Do not assume an existing header paragraph supports a simple `clear()` call. Remove runs or replace the paragraph XML when needed.

### Tracked changes and comments

Real tracked changes and Word comments are not first-class in `python-docx`. Use the bundled OOXML helpers under `ooxml/` and `scripts/` when required.

## 4. Deliverable Content Audit

Before rendering the final CREATE/EDIT result, run the content audit defined in `references/deliverable_content.md`.

At minimum, check for:

- chat leakage;
- process/QA/tool commentary inside the artifact;
- assistant-style framing such as "Based on your request..." or semantic equivalents in the document's language;
- generic introductions or conclusions added only for completeness;
- audience mismatch;
- unsupported claims;
- poor handling of unknown/unconfirmed facts;
- sentences that fail the standalone-document test.

Remove or rewrite anything that fails before visual QA.

## 5. Render and visual QA

After every meaningful authoring batch, use the loop from `tasks/verify_render.md`.

For final delivery:

1. finish substantive authoring;
2. run the Deliverable Content Audit;
3. render the DOCX to page PNGs;
4. inspect every final page at 100% zoom;
5. fix layout defects;
6. re-render after any layout-sensitive or OOXML change;
7. deliver only after the latest render is clean.

If LibreOffice/`soffice` is unavailable, follow the structural fallback described in `SKILL.md` and disclose that visual QA could not be completed.

## 6. Output hygiene

Keep `/mnt/data` clean: final deliverables only unless the user explicitly requests intermediate renders or debug artifacts.

Keep assistant explanations about what changed, why it changed, and what was checked in the final chat response rather than inserting them into the document itself.
