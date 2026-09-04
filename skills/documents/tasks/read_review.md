# Task: Read or visually review an existing DOCX

Use the task mode selected in `SKILL.md`. Reading content and reviewing layout are separate paths.

## 1. Content-only READ_ONLY

Use this path for summarizing, extracting, comparing substantive content, answering questions, or using a document as reference.

- Read only the content needed for the request.
- Do not run the artifact-operation marker.
- Do not modify, normalize, sanitize, or re-export the source.
- Do not render or inspect layout unless visual information is required.

Render only when the user asks about visual characteristics or parsed content is incomplete, garbled, or ambiguous.

## 2. VISUAL_REVIEW

Use this path for questions about:

- page breaks, margins, clipping, overlap, or excessive whitespace;
- typography, hierarchy, font consistency, or line spacing;
- tables, figures, images, wrapping, or truncation;
- headers, footers, and page furniture;
- tracked changes or comments.

Render with:

```bash
python render_docx.py /mnt/data/input.docx --output_dir /mnt/data/out
```

For debugging:

```bash
python render_docx.py /mnt/data/input.docx --output_dir /mnt/data/out --verbose
```

For optional PDF output:

```bash
python render_docx.py /mnt/data/input.docx --output_dir /mnt/data/out --emit_pdf
```

Inspect the pages needed to answer the visual question. Report findings without changing the source unless the user requests fixes.

For final QA of a newly created or edited document, use the stricter all-pages gate in `SKILL.md` and `tasks/verify_render.md`.

## 3. Tracked changes and comments

Tracked insertions and deletions often appear in page renders. Comments often do not.

When comments matter, verify `comments.xml`, anchors, relationships, and content types, or use the bundled comment helpers. A clean render does not prove that comments exist.
