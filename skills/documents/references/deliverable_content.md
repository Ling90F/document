# Deliverable Content Contract

Use this reference for all newly authored or substantially rewritten document content. The rules are language-agnostic.

## 1. Standalone deliverable

A document is a finished artifact for its real audience. It is not a transcript of the assistant-user conversation and not a record of the production process.

Before adding a sentence, ask:

> Would this sentence still belong in the document if the recipient never saw the ChatGPT/Codex conversation?

If not, keep it in chat.

Do not place the following in the artifact unless the requested document type requires them:

- explanations addressed to the user;
- descriptions of what was changed, checked, generated, or formatted;
- tool, script, rendering, or QA status;
- reasons for document-production choices;
- research-process commentary;
- next-step suggestions addressed to the user;
- references to prompts or conversation history.

Write from the perspective of the real author or organization and for the document's intended reader.

## 2. Minimal sufficient explanation

Write only what the reader needs to understand, decide, verify, or act.

State the required fact, action, condition, limitation, decision, instruction, or measured result once, then stop.

Add a reason or consequence only when it is material and not obvious, for example when it:

- changes a decision, obligation, priority, or risk;
- is needed to perform an action correctly;
- explains a non-obvious constraint;
- supplies evidence;
- distinguishes between materially different options;
- is explicitly requested by the user or required by the document type.

Do not turn every statement into a complete argument containing a conclusion, reason, benefit, attitude, assurance, and recommendation.

The goal is not uniformly short prose. Keep technical detail, evidence, caveats, and analysis when they serve the document's purpose. Remove explanation that merely interprets what the reader can already infer.

## 3. Describe mechanics, not inferred benefits

Prefer observable behavior, parameters, conditions, states, and measured results.

Do not automatically append a sentence that says why the preceding action is useful, effective, faster, safer, clearer, or better.

Bad:

> Uploaded images are automatically optimized, significantly reducing file size and improving the browsing experience.

Better:

> Uploaded images are resized to a maximum edge of 1600 px. The WebP result is used only when it is smaller than the source file.

Bad:

> The permission mechanism effectively strengthens system security and prevents unauthorized operations.

Better:

> The API checks permission before execution. Unauthorized requests return HTTP 403.

Do not state performance, quality, security, or efficiency improvements without measurements, identified evidence, or a document-specific need for analysis.

Prefer:

> The test image changed from 4.8 MB to 620 KB.

Avoid:

> The file size was significantly reduced.

Common consequences usually do not need explanation. Examples include compression producing a smaller file, caching reusing a resource, lazy loading delaying off-screen requests, validation rejecting invalid input, and permission checks rejecting unauthorized operations.

## 4. Sentence and paragraph discipline

Give each sentence one primary job:

- state a fact;
- give an instruction;
- record a decision;
- define a term;
- present evidence;
- identify a condition, limitation, or risk;
- make a recommendation.

When several elements are necessary, separate them into sentences, bullets, fields, or labeled sections.

Give each paragraph one controlling point. Do not routinely:

- restate the heading in the first sentence;
- repeat the opening point in the final sentence;
- add a benefit statement at the end of every paragraph;
- add a mini-summary after a short section;
- introduce and conclude every subsection;
- repeat the same rationale in prose, bullets, and tables;
- use symmetrical three-part phrasing merely to sound polished.

Transitions, adverbs, and evaluative modifiers are not banned. Remove them when they add tone rather than information. Words equivalent to "significantly," "effectively," "comprehensively," "fully," "further," or "strongly" require evidence or a real distinction.

Once the necessary information is complete, stop.

## 5. Openings and endings

Start with the document's natural content: title, purpose, background, scope, summary, applicability, or the first substantive section.

Do not add an opening whose only purpose is to say that the content was prepared, organized, revised, or provided in response to the user.

End where the substantive content ends. Do not add a generic summary, reassurance, offer of further help, or polite closing unless the real document type requires one.

Examples to avoid include semantic equivalents of:

- "Based on your request..."
- "The following content has been organized..."
- "This revision makes the following improvements..."
- "The above is for reference."
- "Please contact us if you need anything else."

These are semantic examples, not literal keyword bans.

## 6. Unknown or unconfirmed information

Do not invent facts to make the document look complete.

Express unresolved information in document-native language, such as:

- To be confirmed.
- Not yet specified.
- Subject to confirmation by the responsible authority.
- Subject to final official requirements.
- Subject to the deployed configuration.
- Available source material does not specify this requirement.

Do not write "I could not find this" or "you should ask the platform" unless the requested artifact is itself a research log or advisory note.

## 7. Editing existing documents

For EDIT tasks:

- preserve the established voice, structure, terminology, and level of formality;
- apply this contract to inserted or rewritten content;
- do not rewrite compliant text merely to impose a generic style;
- keep local requests local;
- do not add edit commentary unless comments, tracked changes, or a revision log were requested.

## 8. Content audit and compression pass

Before visual rendering, review every newly authored or substantially rewritten section.

1. **Standalone fit** - Does every sentence belong in the artifact without the conversation?
2. **Audience** - Is the text written for the real reader rather than the ChatGPT/Codex user?
3. **Chat/process leakage** - Are explanations, production notes, tools, QA, or change summaries inside the artifact?
4. **Necessity** - Does each sentence add a required fact, action, condition, decision, instruction, limitation, risk, or measured result?
5. **Explanation load** - Does any sentence or paragraph explain more than the reader needs?
6. **Inferred benefits** - Does text merely state why an already-clear action is useful or better?
7. **Rhetorical overload** - Does a sentence combine conclusion, reason, benefit, assurance, attitude, and recommendation?
8. **Evidence** - Are performance, quality, security, or efficiency claims supported?
9. **Modifiers** - Can unsupported intensifiers or evaluative wording be removed?
10. **Repetition** - Does a paragraph restate its heading, opening sentence, or previous section?
11. **Opening/ending** - Were generic framing or closing sentences added without a document-specific need?
12. **Uncertainty** - Are unresolved facts marked in document-native terms rather than explained as assistant research?
13. **Edit scope** - For an existing document, were compliant content and structure preserved?

Remove, split, or rewrite anything that fails this pass. Do not remove detail that the reader needs to act, verify, assess risk, or understand a non-obvious point.
