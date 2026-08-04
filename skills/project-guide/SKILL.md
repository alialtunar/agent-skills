---
name: project-guide
description: "Write a screenshot-anchored, layered PROJECT_GUIDE.md that explains a codebase end to end: why it exists, UI anatomy, one-turn lifecycle (mermaid), per-component sections with analogies and <details> deep-dives citing real files/lines, real data samples, run instructions, API table, directory map, self-test questions, and known limitations. For 'write a project guide/handbook', 'document this project', onboarding docs."
---

# Project Guide Writer

Produce a single durable `PROJECT_GUIDE.md` that a newcomer can read top-to-bottom in ~15 minutes and come away with a correct, working mental model — while an experienced dev can use the same document as a reference. The format is a proven template (below); the craft is in the writing rules.

## The two-layer rule (applies to every section)

Every section has a **readable surface** (plain prose, one analogy, small tables) and, where depth exists, a folded **`<details><summary>Technical details</summary>`** block. The surface never mentions file paths or line numbers; the details block is *made of* them. A reader who never opens a single `<details>` must still end with a correct model; a reader who opens all of them must find nothing missing.

## Document template (follow this order; drop sections that genuinely don't apply)

1. **Title + one-paragraph thesis** — what this is, in one breath, as a blockquote.
2. **Why does this exist?** — the problem as a small table (gap → consequence), then the solution as a numbered list of 2–4 responsibilities, then ONE load-bearing analogy that will be reused throughout the doc.
3. **UI anatomy** *(if the project has a UI)* — one full screenshot, then a bullet per screen region explaining what it shows and, crucially, the project's thesis as expressed by the UI ("the right panel IS the point: X should not be invisible").
4. **Lifecycle of one unit of work** — trace ONE real, named user action end to end with a mermaid `sequenceDiagram` (≤7 participants). Call out the single most important timing/architecture fact in bold right after it. Details block: exact call chain with file:line, the "why" behind each non-obvious phase.
5. **Component sections** — one per major component. Open with an orientation table if components map to visible tabs/areas (component → tab → its question → lifetime). Each component section: screenshot (if visible in UI) → what it does in plain words → its rules stated crisply ("X holds facts, overwrites on conflict, writes immediately") → one analogy → details block with file paths, thresholds, config values, and design rationale mined from docstrings/comments.
6. **What the data actually looks like** — 2–3 short real (redacted/shortened) JSON/log samples from different layers of the same unit of work, each with a one-line caption naming the layer.
7. **Honest operational notes** — anything disabled by default, env-dependent, or surprising ("feature X is off in Docker because dep Y isn't in requirements; the tab will look empty; here's how to enable"). These notes build more trust than any polished paragraph.
8. **How to run** — exact commands, env file with placeholder keys, gotchas (e.g. "changing the embedding model invalidates existing vectors"). Then an **API endpoint table** (endpoint → what it returns) if there's an API.
9. **Directory map** — annotated tree (one line per entry, comments not restatements of names), ending with the dependency direction stated as a rule ("the agent knows the engine; the engine never knows the agent").
10. **Self-test questions** — 4–6 questions answerable *from the document*, each probing a mechanism rather than trivia ("if tool T is removed, what happens to note N and why?"). Optionally a final details block linking known gaps/open issues if the repo tracks them.

## Writing rules

- **Verify or omit.** Every file path, function name, threshold, config value, and line reference must be grep-verified in the repo at writing time. Line numbers are allowed *only* inside details blocks and only when checked.
- **Method over facts.** When describing stores/memories, state what kind of content belongs and what is banned, and why ("no prices or dates — facts go stale; methods keep").
- **Mine the repo's own voice.** Docstrings, "order matters" comments, bug confessions, and known-issues docs (`*ALGORITHM*.md`, `KNOWN_ISSUES`, ADRs) are the best raw material — quote or paraphrase them with pointers.
- **One analogy per concept, reused consistently.** Introduce the load-bearing analogy in §2 and let component sections extend it rather than inventing unrelated ones.
- **Screenshots:** ask the user to provide them (or capture via a browser tool if available and the app runs). Reference as `img/NN-name.png` with numbered ordering. If no screenshots are possible, write the guide without §3 and without per-component images — never fabricate image references to files that don't exist... unless the user asks for placeholders, then say so explicitly in the doc.
- **Language:** write in the user's language. Keep code identifiers, file paths, and quoted docstrings in their original language.

## Workflow

1. **Recon** — tree + manifests → stack, entry points; read the most critical flow end to end; list components; locate known-issues/self-critique docs.
2. **Ask for screenshots** if the project has a UI and none were provided (one full view + one per panel/tab is enough).
3. **Draft** following the template; keep the surface layer free of jargon.
4. **Verification pass** — grep every claim; fix or delete what you can't confirm.
5. **Self-check** — Would a newcomer who skips all details blocks still be correct? Do the self-test questions have answers in the text? Is anything in the doc flattering rather than true?
6. **Deliver** as `docs/PROJECT_GUIDE.md` (create `docs/img/` for screenshots). Ask before overwriting an existing guide.

A full worked example of the target shape is in `references/example-structure.md` (skeleton with the section order, table shapes, and details-block pattern).
