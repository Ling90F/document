# Deliverable Content Contract

Use this reference for all newly authored document content. The rules are language-agnostic and apply regardless of whether the final document is written in English, Chinese, or another language.

## Core principle

A document artifact is a standalone real-world deliverable, not a transcript of the assistant-user conversation and not a record of the document-production process.

Before inserting any sentence into a document, apply this test:

> If the intended recipient never saw the ChatGPT/Codex conversation, would this sentence still belong in the document?

If the answer is no, do not place the sentence in the document.

## Separate chat content from document content

### Chat content

Keep the following in the assistant's chat response unless the user explicitly requests them inside the artifact:

- explanations addressed to the user;
- descriptions of what was changed, checked, generated, or reformatted;
- reasons for implementation or formatting choices;
- tool, script, rendering, QA, or workflow status;
- suggestions about what the user should do next;
- research-process commentary;
- uncertainty explanations written as assistant-to-user commentary;
- references to the conversation or to the user's prompt.

### Document content

Include only material that belongs in the finished artifact itself, such as:

- business, technical, legal, procedural, policy, analytical, or informational content intended for the real document audience;
- document-native headings, labels, tables, notes, definitions, procedures, findings, decisions, and source statements;
- content that remains appropriate when the artifact is detached from the chat that produced it.

Do not copy chat content into the document merely because it helped explain the work to the user.

## Deliverable voice

Unless the user explicitly requests working notes, meeting minutes, review commentary, a teaching document, or another conversational form, write AUTHORING output in deliverable voice.

Deliverable voice means:

- write from the perspective appropriate to the real document and its intended audience;
- do not address the ChatGPT/Codex user as "you" unless the document itself is genuinely addressed to that person;
- do not mention the assistant, the conversation, prompts, tools, or generation process;
- do not narrate why content was added, removed, reordered, or formatted;
- do not add revision summaries inside the artifact unless revision history is part of the requested document type;
- prefer direct factual, procedural, contractual, technical, or business language;
- preserve the expected voice of the real author, organization, or document type.

Avoid assistant-style framing such as semantic equivalents of:

- "Based on your request..."
- "According to your requirements..."
- "Here is the revised content..."
- "The following section will explain..."
- "In this revision, we changed..."
- "I recommend..."
- "You can..."
- "If you need anything else..."
- "For your convenience..."
- "I checked..."
- "This document has been optimized..."

These examples are semantic patterns, not literal keyword bans. A similar phrase is allowed when it genuinely belongs to the real subject matter or document context.

## Opening discipline

Do not create a generic assistant-style opening merely to make the artifact feel complete.

Avoid openings whose only function is to describe the user's request, the drafting task, or the fact that content was organized.

Prefer to begin directly with the document's natural opening structure, such as:

- title;
- purpose;
- background;
- scope;
- executive summary;
- applicability;
- first substantive section.

A short introduction is appropriate only when the real document needs one.

## Ending discipline

Do not add a conversational closing merely because the document is ending.

Avoid generic endings equivalent to:

- "The above is for reference."
- "Please contact us if you have any questions."
- "Further improvements can be made if needed."
- "I hope this document is helpful."
- "Additional content can be added later."

End the artifact where its substantive content naturally ends, unless the real document type requires a closing, signature block, approval section, appendix, source list, or similar element.

## Unknown or unconfirmed information

Do not invent or infer unsupported facts to make a document appear complete.

When a fact is genuinely unresolved, express the uncertainty in document-native language appropriate to the artifact, for example:

- To be confirmed.
- Not yet specified.
- Subject to confirmation by the responsible platform or authority.
- Subject to final official requirements.
- Subject to the actual deployed configuration.
- Available public materials do not specify this requirement.

Do not expose the assistant's research process inside the artifact, such as "I could not find this" or "you should ask the platform," unless the document itself is explicitly a research log or advisory note.

## Editing existing documents

For EDIT tasks:

- preserve the document's established voice, structure, terminology, and level of formality;
- apply this contract primarily to newly inserted or rewritten content;
- do not rewrite compliant existing prose merely to impose a generic house style;
- keep the modification scope local when the user's request is local;
- do not add explanatory commentary about the edit into the document unless the user explicitly requests tracked review notes, comments, or a revision log.

## Deliverable Content Audit

Before final rendering of a CREATE or EDIT task, perform a content-only audit separate from visual QA.

Check the finished artifact for:

1. **Chat leakage** - assistant-to-user explanations entered the document.
2. **Process leakage** - the artifact describes what the assistant changed, checked, generated, or executed.
3. **Conversational framing** - unnecessary assistant-style introductions, recommendations, or direct user guidance.
4. **Artificial introduction** - an opening exists only to explain the drafting task rather than the document subject.
5. **Artificial conclusion** - a generic summary or polite closing was added without a document-specific reason.
6. **Audience mismatch** - the artifact speaks to the ChatGPT/Codex user instead of its actual intended audience.
7. **Unsupported claims** - facts, requirements, sources, or conclusions were added without adequate support.
8. **Uncertainty handling** - unresolved facts are expressed in document-native terms instead of exposing assistant research commentary.
9. **Standalone test** - every sentence still makes sense and belongs in the artifact if the recipient never sees the conversation that produced it.

Remove or rewrite anything that fails this audit before visual rendering and final delivery.
