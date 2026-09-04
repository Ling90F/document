---
name: documents
description: Read, create, edit, redline, comment on, and visually review `.docx`, Word, and Google Docs-targeted artifacts. Use a lightweight read-only path for content questions; require deliverable-content, render, and visual QA for new or modified documents.
---

# Documents Skill

Use this skill for Word/DOCX and Google Docs-targeted document work in the container environment.

## 1. Route the task

Classify the request before acting.

### READ_ONLY

Use for reading, understanding, summarizing, extracting, comparing substantive content, answering document-based questions, or using a document as reference.

- Do not run `mark_artifact_operation_started.mjs`.
- Do not modify, normalize, sanitize, re-export, or otherwise mutate the source.
- Do not render or inspect layout by default.
- Render only when the user asks about appearance/layout or parsed content is incomplete, garbled, or ambiguous.

Requests such as "read this," "look at this document," "summarize it," or "use this as reference" are READ_ONLY unless the user also requests a document change or visual review.

### VISUAL_REVIEW

Use when the user asks about layout, typography, pagination, tables, images, clipping, spacing, headers/footers, tracked changes, or other visual characteristics.

- Render or inspect page images as needed.
- Report findings without changing the source unless the user requests fixes.
- Verify comments structurally when relevant; headless rendering may not show them.

### CREATE

Use when the user asks for a new DOCX, Word file, or Google Docs-targeted deliverable.

### EDIT

Use when the user asks to modify an existing document. Preserve the existing structure, terminology, voice, and design unless the user requests broader changes. Prefer the smallest correct edit.

### AUTHORING workflow

CREATE and EDIT use this sequence:

1. Clarify only consequential missing requirements.
2. Run the artifact-operation marker exactly once immediately before the first authoring command.
3. Select or follow a template/design preset when applicable.
4. Read `references/deliverable_content.md`.
5. Create or edit the document.
6. Run the content audit and compression pass from `references/deliverable_content.md`.
7. Render the DOCX, inspect every page, fix defects, and re-render.
8. Deliver only the requested final artifact.

### Fallback

When intent is ambiguous, choose READ_ONLY unless the user explicitly requests a new deliverable or a mutation to an existing document.

## 2. Runtime and operation marker

Use Codex workspace dependencies for DOCX work. Resolve them through the workspace dependency loader or runtime skill and treat the returned Node/Python runtimes and package directory as authoritative.

- Do not use system `node`, system `python`, global npm packages, or repo-local installs.
- Run builders and helpers from a writable workspace or temp directory, not from a managed dependency directory.
- Bundled Python/OOXML helpers may be used when the higher-level surface is incomplete.

Immediately before the first CREATE/EDIT authoring command, run successfully exactly once:

```bash
node container_tools/mark_artifact_operation_started.mjs --operation-kind create --expected-output-count 1 --output-format docx
```

For EDIT, replace `create` with `edit`. Adjust the expected count and output format to match the requested deliverables. Do not run this marker for READ_ONLY or pure VISUAL_REVIEW.

## 3. Authoring content contract

Before writing or rewriting substantive document content, read:

`references/deliverable_content.md`

The artifact must stand on its own. Keep chat explanations, production notes, tool/QA narration, and next-step guidance out of the document unless the requested document type requires them.

Use minimal sufficient explanation:

- state the necessary fact, action, condition, limitation, decision, or measured result;
- do not append obvious reasons, inferred benefits, reassurance, or evaluative conclusions;
- retain technical detail when the reader needs it to decide, verify, or act;
- do not invent missing facts.

Complete the content audit and compression pass before render QA.

## 4. Templates and design

### Template selection

For a new document, open the template picker when the user has not supplied a template, reference document, or clear visual direction. Also open it when the user asks to browse or upload templates.

Do not open the picker when the user declines templates or requests a connected-source design search.

Call `list_artifact_templates({artifactKind, request})` with `artifactKind: "document"` or `"google-docs"`. Rank by relevance, include a useful mix of styles, favor personal/shared templates on ties, and pass returned `skillName` values unchanged to `choose_artifact_template({artifactKind, request, templates})` once. Set `includeAllTemplates: true` only when the user requests the full catalog.

Save an uploaded reference only when `saveForFutureUse` is true. Continue without a template if the picker is declined, cancelled, unavailable, or fails.

### Template following

When an attached or retained DOCX controls a new document's design, read `template-distill.md` and then `template-create.md`. Keep the reference file and the task-local `$TMP_DIR/artifact.md` together throughout authoring.

The retained reference is the design authority. Do not apply a generic preset, page baseline, or header pattern unless the user asks to depart from it. The authoring content and render gates still apply.

### Design presets

Outside template-following mode, read `references/design_presets.md` and choose exactly one preset for new documents and major rewrites:

- `google_docs_default` for native Google Docs-targeted output;
- `standard_business_brief` for formal memos, decision briefs, RFI-style responses, and board/business documents;
- `compact_reference_guide` for dense operator guides, launch guides, negotiation briefs, and checklists;
- `narrative_proposal` for grants, proposals, and longer persuasive documents;
- a documented archetype alias when it fits better.

If creating a non-Google-Docs first-page header, cover, or title block, also read `references/header_templates.md`.

Resolve the selected preset into explicit numeric tokens. Do not rely on Word defaults for preset-controlled page geometry, typography, spacing, headings, lists, tables, callouts, headers/footers, or colors.

Unless a template or preset says otherwise, use US Letter portrait, 1-inch margins, 9360 DXA usable width, real Word styles, real numbering definitions, and explicit DXA table widths.

### Structure

Choose the lightest form that serves the reading task:

- prose for narrative or explanation;
- callouts for decisions, warnings, or constraints;
- numbered steps for sequences;
- bullets or checklists for grouped requirements/actions;
- definition lists for metadata and key facts;
- tables only for repeated comparable fields;
- form layouts for questionnaires;
- source notes or appendices for evidence.

Do not use tables as general layout containers or to package ordinary prose.

For tables, set deliberate widths and cell margins; keep `tblW`, `tblInd`, `tblGrid`, and every `tcW` consistent; set table indent to the start-cell margin token (`120` DXA by default); avoid autofit, percentage widths, fixed row heights, and clipping. Use `scripts/table_geometry.py` or equivalent logic.

Use real Word numbering. Do not fake bullets or numbered lists with typed characters.

## 5. Google Docs-targeted output

For a net-new Google Docs request:

1. Create a local DOCX.
2. Sanitize the title block.
3. Render and visually verify the sanitized DOCX.
4. Import it with `mcp__codex_apps__google_drive_import_document` using `upload_mode: "native_google_docs"`.

Before render/import:

```bash
python scripts/google_docs_title_sanitize.py input.docx --out sanitized.docx
python scripts/google_docs_title_sanitize.py sanitized.docx --check
```

Use the sanitized file for both render QA and import.

For Google Docs-targeted documents:

- use `google_docs_default` unless the user explicitly requests another visual system;
- never use Word's built-in `Title` paragraph style;
- create a plain title paragraph with explicit tokens; for `google_docs_default`, use Arial 26 pt, black, normal weight, 0 pt before, and 3 pt after;
- fail the audit if the title contains `w:pBdr`, a bottom border, underline, horizontal rule, or other Word-template residue.

Do not use Computer Use, Browser Use, blank-document construction, or another direct-to-Docs path for a net-new Google Doc unless the user explicitly requests it. In that case, state first that DOCX import is expected to produce the best quality.

If the Google Drive plugin is unavailable, ask the user to install `google-drive@openai-curated`. If the plugin is installed but the import action is missing, ask the user to refresh or reinstall it.

## 6. Authoring render gate

This gate applies only to newly created or modified DOCX deliverables.

Before delivery:

1. Run `render_docx.py` to generate `page-<N>.png` files.
2. Inspect every page at 100% zoom.
3. Check text, tables, images, page breaks, headers/footers, glyphs, spacing, and alignment.
4. Fix every visible defect.
5. Re-render after any layout-sensitive or OOXML change.
6. Deliver only after the latest render is clean.

Use:

```bash
python render_docx.py /mnt/data/input.docx --output_dir /mnt/data/out
python render_docx.py /mnt/data/input.docx --output_dir /mnt/data/out --verbose
python render_docx.py /mnt/data/input.docx --output_dir /mnt/data/out --emit_pdf
```

If rendering fails only because LibreOffice/`soffice` is unavailable, perform structural QA, disclose that visual QA was not completed, and do not claim the render gate passed. Fix all other rendering failures rather than guessing.

Rendered PNGs and PDFs are internal QA artifacts unless the user asks for them. Rendering does not reliably prove that comments exist; verify comments structurally.

## 7. Clarification rules

Ask questions only when a consequential requirement cannot be inferred safely, especially for new documents or major rewrites.

Do not delay ordinary edits, conversions, or read-only work with unnecessary questions. Unresolved labels, placeholders, and question marks remain user-owned; use placeholders or document-native uncertainty instead of inventing facts.

Use `request_user_input` once when available. Put the best suggestion first and label it `(Recommended)`, provide one good alternative, and use `Use your judgment` as the final option. If no answer arrives, proceed with the recommended choice.

## 8. Quick utilities

```bash
# Strip comments
python scripts/comments_strip.py input.docx --out no_comments.docx

# Accept tracked changes
python scripts/accept_tracked_changes.py input.docx --mode accept --out accepted.docx

# Accessibility audit
python scripts/a11y_audit.py input.docx

# Redact sensitive text
python scripts/redact_docx.py input.docx redacted.docx --emails --phones
```

## 9. Package routing

### Core references

- `references/deliverable_content.md` - required for newly authored content.
- `references/design_presets.md` - design tokens, archetypes, and audits.
- `references/header_templates.md` - first-page header/title-block patterns.

### Primary task guides

- content-only reading and visual review: `tasks/read_review.md`
- create/edit: `tasks/create_edit.md`
- render verification: `tasks/verify_render.md`
- accessibility: `tasks/accessibility_a11y.md`
- comments: `tasks/comments_manage.md`
- tracked-change cleanup: `tasks/clean_tracked_changes.md`
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

## 10. Final response citations

Place `:codex-file-citation{...}` inline in prose without wrapping it in backticks or a code block.

- CREATE/EDIT: cite each final DOCX exactly once with `purpose="output"`.
- Q&A/no-op: use `purpose="source"`; do not edit or re-export merely to cite.
- For page-specific evidence, use only a page number verified against the latest inspection.
- Do not cite QA intermediates unless the user asks for them.
