# Task: Read / review an existing DOCX

Use this task guide for READ_ONLY work and for explicit VISUAL_REVIEW work. Do not assume that every read/review request requires rendering.

## 1. Content-only READ_ONLY workflow

Use this path when the user only wants to read, understand, summarize, extract, compare substantive content, answer questions, or use the document as context/reference.

Default behavior:

- Do not run `mark_artifact_operation_started.mjs`.
- Do not run `render_docx.py`.
- Do not generate page PNGs.
- Do not inspect typography, spacing, pagination, table geometry, style consistency, accessibility, or other visual/layout properties.
- Do not modify, normalize, sanitize, re-export, or otherwise mutate the source document.
- Read only the relevant content needed to answer the user's request.

Render or inspect page images only when either:

1. the user explicitly asks about visual layout, formatting, page breaks, tables, images, typography, clipping, headers/footers, or appearance; or
2. extracted/parsed content is missing, garbled, ambiguous, or insufficient to answer reliably.

A request such as "read this", "look at this document", "summarize it", "tell me what it says", or "use this as reference" is content-only READ_ONLY unless the user asks for visual review or document mutation.

## 2. VISUAL_REVIEW workflow

Use this path only when visual inspection is actually required.

### What to review

Depending on the user's question, inspect:

- layout: page breaks, margins, clipping, overlap, excessive whitespace;
- typography: heading hierarchy, font consistency, line spacing;
- tables/figures: alignment, legibility, truncation, wrapping;
- headers/footers and page furniture;
- tracked changes: whether insertions/deletions display as expected;
- comments: whether they exist structurally, even if they do not render.

### Preferred renderer

```bash
python render_docx.py /mnt/data/input.docx --output_dir /mnt/data/out
```

For debugging:

```bash
python render_docx.py /mnt/data/input.docx --output_dir /mnt/data/out --verbose
```

Optional PDF output:

```bash
python render_docx.py /mnt/data/input.docx --output_dir /mnt/data/out --emit_pdf
```

### Visual-review success criteria

- required page images exist;
- page count is plausible;
- inspect the pages needed to answer the user's visual question;
- for final QA of an authored/edited deliverable, follow `tasks/verify_render.md` and inspect every final page;
- report findings without changing the source unless the user explicitly requests fixes.

## Notes on redlines vs comments

- Tracked insertions/deletions often appear in rendered PDF/page images.
- Comments frequently do not render in headless LibreOffice output.
- Rendering is therefore not proof that comments exist.
- When comments matter, perform a structural check using `ooxml/comments.md` or the bundled comment helpers.

## Large documents

For content-only READ_ONLY work, read only the sections necessary to answer the question.

For explicit VISUAL_REVIEW of a large document, inspect the pages relevant to the user's question first. For final delivery QA of a newly created or edited DOCX, follow the stricter authoring render gate in `SKILL.md` and `tasks/verify_render.md`.
