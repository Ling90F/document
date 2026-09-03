---
name: documents
description: Read, create, edit, redline, comment on, and visually review `.docx`, Word, and Google Docs-targeted document artifacts. Content-only reading uses a lightweight read-only workflow; newly created or modified deliverables use strict content, render, and visual-QA gates.
---

# Documents Skill (Read • Create • Edit • Redline • Comment • Visual Review)

Use this skill for Word/DOCX and Google Docs-targeted document work in the container environment.

## Document Work Modes

Classify the request before doing document work. Use exactly one primary mode unless the task clearly combines them.

### READ_ONLY

Use READ_ONLY when the user only wants to:

- read or understand a document;
- summarize document content;
- answer questions based on a document;
- extract facts, text, headings, tables, or other content;
- compare substantive content across documents;
- use an attached document only as context or reference;
- inspect what a document says without changing it.

READ_ONLY defaults:

- Do **not** run `mark_artifact_operation_started.mjs`.
- Do **not** run `render_docx.py` by default.
- Do **not** generate page PNGs by default.
- Do **not** perform typography, spacing, pagination, table-geometry, style, accessibility, or other visual/layout QA unless the user explicitly asks for it.
- Do **not** edit, normalize, sanitize, re-export, or otherwise mutate the source document.
- Read only the content necessary to answer the user's question.
- Use `tasks/read_review.md` as the default read-only workflow.

Rendering or page-image inspection is allowed in READ_ONLY only when:

1. the user explicitly asks about visual layout, pagination, formatting, tables, images, or appearance; or
2. extracted/parsed content is missing, garbled, ambiguous, or insufficient to answer reliably.

A request to "look at", "read", "check the content of", "understand", or "use this document as reference" is READ_ONLY unless the user explicitly requests document creation, mutation, or visual/layout review.

### CREATE

Use CREATE when the user asks for a new DOCX, Word file, or Google Docs-targeted deliverable.

CREATE is an AUTHORING mode. Before writing substantive artifact content, read and follow:

`references/deliverable_content.md`

Then apply the design/template rules in this skill, run the Deliverable Content Audit, render the finished DOCX, inspect every page, and iterate until the final artifact passes both content and visual QA.

### EDIT

Use EDIT when the user asks to modify an existing document.

EDIT is an AUTHORING mode. Preserve the source document's established voice, structure, terminology, and design unless the user explicitly requests broader changes. Prefer minimal, local edits over rewriting entire sections.

Before inserting or rewriting substantive content, read and follow:

`references/deliverable_content.md`

The content contract applies primarily to newly inserted or rewritten content. Do not rewrite compliant existing prose merely to impose a generic style.

### VISUAL_REVIEW

Use VISUAL_REVIEW when the user explicitly asks to inspect layout, typography, pagination, table appearance, image placement, clipping, spacing, headers/footers, or other visual characteristics.

- Rendering is appropriate in this mode.
- Do not modify the document unless the user also asks for changes.
- If the user asks only for findings, report findings and leave the source untouched.

### Mode fallback

When in doubt between READ_ONLY and AUTHORING, choose READ_ONLY unless the user explicitly requests a new document deliverable or a mutation to an existing document.

## Tools + Contract Requirements

- Use Codex workspace dependencies for DOCX artifact work: resolve them through the workspace dependency loader or runtime skill, then treat the returned Node/Python runtimes and package directory as authoritative. Do not use system `node`, system `python`, global npm packages, or repo-local installs.
- For deterministic OOXML edits, it is acceptable to use the bundled Python/OOXML helpers in this skill package when the higher-level surface is incomplete.
- Run builder/helper files from a writable workspace or temp directory, not from the managed dependency directory itself.
- Final user-facing responses should describe only the requested result. Do not link QA intermediates unless the user explicitly asks for them.

Immediately before the first CREATE/EDIT authoring command, run `mark_artifact_operation_started.mjs` successfully exactly once. Do not run it for READ_ONLY or pure VISUAL_REVIEW.

For creation:

```bash
node container_tools/mark_artifact_operation_started.mjs --operation-kind create --expected-output-count 1 --output-format docx
```

For editing, replace `create` with `edit` and adjust the expected count/output format to match the requested deliverables.

## Artifact Content Boundary

For all AUTHORING tasks, the artifact is a standalone real-world deliverable, not a transcript of the assistant-user conversation and not a record of the production process.

Before inserting substantive content, read:

`references/deliverable_content.md`

Keep assistant-to-user explanations, work summaries, implementation notes, QA status, tool details, and next-step suggestions in chat unless the requested document type explicitly requires them.

Before visual rendering, CREATE and EDIT tasks must pass the Deliverable Content Audit defined in `references/deliverable_content.md`.

## Artifact Template Selection

For a new document, open the template selection picker when the user has not provided a template, reference document, or clear visual direction. Also open it when the user asks to browse or upload templates.

Do not open the picker if the user declines templates or requests a connected-source design search. Subject matter, audience, company name, and source files alone do not constitute a visual template.

Call `list_artifact_templates({artifactKind, request})` with `artifactKind: "document"`, or `"google-docs"` for Google Docs requests. Rank by relevance, favoring personal/shared templates on ties, and pass returned `skillName` values unchanged to `choose_artifact_template({artifactKind, request, templates})` once.

Continue without a template if the picker is declined, cancelled, unavailable, or fails.

## Google Docs-targeted output

For a net-new Google Docs request, first create and visually verify a local `.docx`. Then import the sanitized DOCX through the Google Drive document import action using native Google Docs mode.

Before rendering or importing a Google Docs-targeted DOCX, run:

```bash
python scripts/google_docs_title_sanitize.py input.docx --out sanitized.docx
python scripts/google_docs_title_sanitize.py sanitized.docx --check
```

Use the sanitized DOCX for render QA and native Google Docs import.

For Google Docs-targeted output:

- use `google_docs_default` unless the user explicitly requests another visual system;
- never create the title with Word's built-in `Title` paragraph style;
- create a plain title paragraph and apply explicit tokens;
- ensure the title has no `w:pBdr`, bottom border, underline, horizontal rule, or Word-template residue.

If the required Google Drive import capability is unavailable, use the appropriate plugin-install or refresh flow rather than silently substituting another delivery path.

## Template Following

When an attached or retained DOCX controls the design of a new document, read `template-distill.md` and then `template-create.md`.

In template-following mode, the retained reference is the design authority. Do not apply a generic design preset, page baseline, or header pattern unless the user explicitly asks to depart from the template.

The AUTHORING content gate and render gate still apply.

## Non-negotiable for AUTHORING: render → inspect PNGs → iterate

This shipping gate applies to newly created or modified DOCX deliverables. It does **not** apply to ordinary READ_ONLY tasks.

For CREATE and EDIT, you do not know a DOCX is satisfactory until it has been rendered and visually inspected.

Before delivering any newly created or modified DOCX:

1. Run the Deliverable Content Audit from `references/deliverable_content.md`.
2. Run `render_docx.py` to produce `page-<N>.png` images (optionally a PDF with `--emit_pdf`).
3. Open and inspect every page at 100% zoom.
4. Check for clipping, overlap, missing glyphs, broken tables, spacing drift, page-break problems, image placement issues, and header/footer defects.
5. Fix any defect and re-render.
6. Repeat until the latest render is clean.

If rendering fails only because LibreOffice/`soffice` is unavailable, it is acceptable to return the requested DOCX after structural QA, but clearly state that visual render QA could not be completed. If rendering fails for another reason, fix rendering rather than guessing.

Rendered PNGs/PDFs are internal QA artifacts unless the user explicitly asks for them.

## Design Preset Contract

Outside template-following mode, choose one design preset for new DOCX creation and major rewrites unless the user explicitly requests another visual system.

Before drafting, read `references/design_presets.md` and select one preset:

- `google_docs_default` for native Google Docs-targeted output;
- `standard_business_brief` for formal memos, decision briefs, RFI-style responses, and board/business documents;
- `compact_reference_guide` for dense operator guides, launch guides, negotiation briefs, and checklists;
- `narrative_proposal` for grants, proposals, and longer persuasive documents;
- use a documented archetype alias when it fits more closely.

Resolve the selected preset into explicit numeric tokens. Do not rely on Word defaults for preset-controlled page geometry, type scale, paragraph rhythm, heading hierarchy, list indents, table geometry, callouts, headers/footers, or color treatment.

Baseline geometry unless a template/preset says otherwise:

- US Letter portrait;
- 1 inch margins;
- 9360 DXA usable width;
- real Word paragraph styles;
- real Word numbering definitions;
- explicit DXA table widths.

### Tables

Use tables only for genuine row/column information with repeated comparable fields.

For tables:

- compute deliberate column widths;
- use explicit Word geometry so `tblW`, `tblInd`, `tblGrid`, and every `tcW` agree;
- avoid autofit, percentage widths, fixed row heights, and tables used only as layout hacks;
- allow rows to grow naturally;
- use adequate cell padding and line spacing;
- align short values deliberately and narrative text appropriately;
- repeat header rows when tables span pages.

Use `scripts/table_geometry.py` or equivalent logic when exact geometry is required.

### Lists

Use real Word numbering definitions. Do not fake bullets or numbering with Unicode bullet characters, hyphen-prefixed paragraphs, manually typed numbers, or newline-separated list items inside a single paragraph.

### Form factor selection

Choose the lightest form that best serves the reading task:

- prose section for narrative/explanation;
- lead callout for decisions or key takeaways;
- numbered steps for sequences/workflows;
- grouped bullets for loose factors/requirements;
- checklist for actionable acceptance items;
- note box for warnings or constraints;
- definition list for metadata or key facts;
- table for repeated comparable records;
- form layout for questionnaires;
- source list/notes/appendix for evidence and references.

Do not package normal prose into tables merely for visual variety.

## Editing Existing Documents

For EDIT tasks:

- preserve the original document unless the user requests a redesign;
- prefer inline/local replacements over rewriting full paragraphs;
- keep the original structure unless a restructure is necessary;
- do not expand a narrow edit request into a broad rewrite;
- use comments or tracked changes only when requested or genuinely required by the task;
- apply `references/deliverable_content.md` to all newly inserted or rewritten material.

## Default Workflows

### READ_ONLY

1. Identify the user's content question.
2. Read only the relevant document content.
3. Answer the question.
4. Do not render unless visual information is required.
5. Do not modify or re-export the source.

### CREATE

1. Clarify only consequential missing requirements.
2. Select/follow a template or design preset as applicable.
3. Read `references/deliverable_content.md`.
4. Author the deliverable.
5. Run the Deliverable Content Audit.
6. Render to PNGs and inspect every page.
7. Fix and repeat until both content and visual QA pass.
8. Deliver only the requested final artifact.

### EDIT

1. Read the existing document and the requested change scope.
2. Preserve the established voice/design and make the smallest correct edits.
3. Apply `references/deliverable_content.md` to inserted/rewritten content.
4. Run the Deliverable Content Audit.
5. Render and inspect every page affected by the edit, plus enough surrounding pages to detect layout regressions; for final delivery, inspect all pages unless the artifact is too large and the task-specific guide defines a safe strategy.
6. Fix and repeat until clean.
7. Deliver the modified artifact.

### VISUAL_REVIEW

1. Render the document or inspect existing page images.
2. Review the requested visual characteristics.
3. Report findings.
4. Do not mutate the source unless the user asks for fixes.

## Documents Clarification Questions

Ask clarification questions for new documents or major rewrites only when topic, audience, purpose, or another consequential requirement cannot be inferred safely.

For ordinary edits, conversions, READ_ONLY work, or sufficiently specified tasks, proceed without unnecessary questions.

Unresolved factual labels, placeholders, or question marks remain user-owned: do not invent facts merely to complete the document. Use placeholders or document-native uncertainty language when appropriate.

## Visual Review Commands

Use the packaged renderer:

```bash
python render_docx.py /mnt/data/input.docx --output_dir /mnt/data/out
python render_docx.py /mnt/data/input.docx --output_dir /mnt/data/out --verbose
python render_docx.py /mnt/data/input.docx --output_dir /mnt/data/out --emit_pdf
```

For AUTHORING final QA:

- PNGs must exist for every page;
- page count must match expectations;
- inspect every final page at 100% zoom;
- verify no clipping, overlap, broken tables, missing glyphs, or misplaced headers/footers.

Rendering is useful for layout and tracked changes, but it is not reliable proof that comments exist. Verify comments structurally when relevant.

## Quick Start Utilities

```bash
# Google Docs title sanitizer
python scripts/google_docs_title_sanitize.py input.docx --out sanitized.docx
python scripts/google_docs_title_sanitize.py sanitized.docx --check

# Render DOCX to PNGs
python render_docx.py input.docx --output_dir out

# Strip comments
python scripts/comments_strip.py input.docx --out no_comments.docx

# Accept tracked changes
python scripts/accept_tracked_changes.py input.docx --mode accept --out accepted.docx

# Accessibility audit
python scripts/a11y_audit.py input.docx

# Redact sensitive text
python scripts/redact_docx.py input.docx redacted.docx --emails --phones
```

## Package Routing

### Core references

- `references/deliverable_content.md` - required content contract for CREATE and newly authored EDIT content.
- `references/design_presets.md` - preset tokens, archetype aliases, and style audit guidance.
- `references/header_templates.md` - first-page header/title-block patterns.

### Primary task guides

- READ_ONLY/content Q&A: `tasks/read_review.md`
- CREATE/EDIT: `tasks/create_edit.md`
- visual/render verification: `tasks/verify_render.md`
- accessibility: `tasks/accessibility_a11y.md`
- comments: `tasks/comments_manage.md`
- tracked changes cleanup: `tasks/clean_tracked_changes.md`
- style normalization: `tasks/style_lint_normalize.md`
- tables/spreadsheets: `tasks/tables_spreadsheets.md`
- images/figures: `tasks/images_figures.md`
- sections/layout: `tasks/sections_layout.md`
- TOC/navigation: `tasks/toc_workflow.md`, `tasks/navigation_internal_links.md`
- captions/cross-references: `tasks/captions_crossrefs.md`
- privacy/redaction: `tasks/privacy_scrub_metadata.md`, `tasks/redaction_anonymization.md`
- forms/protection: `tasks/forms_content_controls.md`, `tasks/protection_restrict_editing.md`
- multi-document merge: `tasks/multi_doc_merge.md`
- compare/diff: `tasks/compare_diff.md`
- templates/style packs: `tasks/templates_style_packs.md`
- watermarks: `tasks/watermarks_background.md`
- footnotes/endnotes: `tasks/footnotes_endnotes.md`

### OOXML references

- `ooxml/tracked_changes.md`
- `ooxml/comments.md`
- `ooxml/hyperlinks_and_fields.md`
- `ooxml/rels_and_content_types.md`

### Key scripts

- `render_docx.py` - canonical DOCX → PNG renderer.
- `scripts/render_and_diff.py` - render + page diff.
- `scripts/table_geometry.py` - exact Word table geometry.
- `scripts/google_docs_title_sanitize.py` - Google Docs title cleanup/audit.
- `scripts/comments_add.py`, `comments_extract.py`, `comments_apply_patch.py`, `comments_strip.py` - comment lifecycle.
- `scripts/add_tracked_replacements.py`, `accept_tracked_changes.py` - tracked changes.
- `scripts/a11y_audit.py` - accessibility audit/fixes.
- `scripts/privacy_scrub.py`, `redact_docx.py` - privacy/redaction.
- `scripts/internal_nav.py`, `insert_toc.py`, `insert_ref_fields.py` - navigation and references.

## Quality Reminders

- Do not ship visible layout defects.
- Do not leak assistant chat, process commentary, or tool/QA narration into the document.
- Do not leak tool citation tokens into the DOCX; convert them to normal human-readable citations when appropriate.
- Do not invent unsupported facts to make a document appear complete.
- Prefer ASCII punctuation where equivalent punctuation avoids renderer inconsistency.

## Final Response Citations

Place `:codex-file-citation{...}` inline in prose without wrapping it in backticks or a code block.

- CREATE/EDIT: cite each final DOCX exactly once with `purpose="output"`.
- Q&A/no-op: use `purpose="source"`; do not edit or re-export merely to cite the source.
- For page-specific evidence, use only a page number verified against the latest inspection.
- Do not cite QA intermediates unless the user asks for them.
