---
name: codebase-explainer
description: "Explain a codebase as an interactive three-altitude HTML guide (overview, driveable simulation, annotated code) or a screenshot-anchored Markdown guide. For onboarding and how-does-X-work questions."
---

# Codebase Explainer

Goal: the reader ends up with a running mental model of the system — they can picture the machine working, recognize it on their own screen, and drill into the real code of any part without being buried in it.

Two deliverable modes, same content standards. Pick by destination:
- **HTML mode** — an interactive artifact for reading in chat/browser (three-altitude ladder below).
- **Markdown mode** — a guide that lives in the repo (`docs/PROJECT_GUIDE.md`) and renders natively on GitHub. Use when the user wants documentation, not just an explanation.

## Content standards (both modes)

- **Match the reader, not the repo.** Jargon from the codebase (internal component names, technical terms) never appears in the guide unless the reader is a developer. For everyone else, translate each term once into a plain word and use only the plain word after that. Short sentences. One idea per sentence.
- **Feynman trio** per concept: plain language; one analogy that reflects the mechanism's real behavior; one named concrete scenario traced end to end and reused throughout.
- **Real screenshots beat redrawn diagrams.** If the app has a UI, anchor the explanation to screenshots of the *running* product: capture them via browser tools if the app can be run, otherwise ask the user for them. Store under `docs/img/`, reference each from the section it explains, and point at specifics ("the 758/6000 bar is the history budget"). Map every visible element (tabs, badges, counters) to its code source.
- **Real data samples.** Show short, real (or realistically shaped, clearly-marked) payloads: a log line, a trace record, an event list. Nothing teaches a data model faster than one honest JSON sample.
- **Ground truth or nothing.** grep-verify every claim. Mine docstrings and comments for design rationale — bug confessions and "order matters" comments are gold; quote them.
- **Code excerpts are short.** 2–8 lines, gaps as dim `# …`, copied not paraphrased.
- **Self-check questions.** End with 3–5 "ask yourself" questions whose answers are derivable from the guide (e.g. "if tool X is removed, what happens to notes bound to it?"). This converts reading into retrieval practice.
- **Honest limitations.** If the repo documents known issues/TODOs, surface them in a collapsible "known gaps" section with references. A guide that admits weaknesses is trusted on everything else.
- **Calm visual design.** Light background, one accent (+ one secondary for async/background flows), ~760–880px width, no glow effects. Numbered step tags so the reader always knows where they are.

## HTML mode — the three-altitude ladder

One file, vanilla JS, no external deps. Working reference: `references/interactive-example.html`.

1. **🛰 Overview (2 min)** — what problem, what essence, one analogy. Screenshots welcome here. Ends with a button dropping into the simulation.
2. **🚁 Simulation (5 min)** — spatial map of the real topology (nodes labeled with real file names; UI-tab names where relevant). The reader drives one real scenario with Next/Back: animated packet on the edges, active nodes highlight, and per step a **two-line narration** (bold what + muted why) plus a **data payload panel** (≤3 lines of what actually travels on that wire). Each step card carries "🔬 see this step's code".
3. **🔬 Code (10 min)** — one section per step: short real excerpt, inline annotations, the design decision behind it, and "↩ return to this step in the simulation" (restores that exact step). Close with self-check questions.

The two-way jumps are the core: curiosity in the simulation is satisfied at ground level three seconds later, and ground level never loses the thread back to the running machine.

## Markdown mode — the repo guide

Structure (adapt, don't pad — cut any section with nothing real to say):

1. **Why this project exists** — the problem, as a small table if it has clean facets; the essence in one bolded sentence; the analogy.
2. **UI anatomy** — one full screenshot; walk the regions; state the thesis the UI embodies if there is one.
3. **Lifecycle of one scenario** — a mermaid `sequenceDiagram` of the chosen scenario + the one critical timing insight (what the user does/doesn't wait for). `<details>` for call-chain specifics.
4. **Component tour** — per major component: what question it answers, its screenshot, plain explanation + analogy, then `<details>Technical details</details>` with file paths, thresholds, and rationale. A small table comparing components (type / question / lifetime) earns its place here.
5. **Real data samples** — 2–3 short JSON/log excerpts with one-line captions.
6. **How to run** — commands, env vars, endpoints table. Only what's verified.
7. **Directory map** — annotated tree + the dependency direction rule in one sentence.
8. **Ask yourself** — 3–5 questions. Optional `<details>` with known gaps.

## Workflow

0. **Audience**: Before anything else, determine who the reader is and how technical they are. If the user hasn't said, ask one question with three options: developer onboarding, a plain-language explanation for a non-expert, or **terse mode** for an experienced developer who wants maximum signal per word. This choice controls everything below — for a non-expert, drop code excerpts, file paths, JSON samples and self-check answers that require reading code; keep only the analogy, the scenario, the screenshots and the flow. For terse mode: prose collapses to caveman-style fragments — no filler, no transitions, one fact per line ("New ref each render. useMemo it."); analogies drop to at most one; structure, diagrams, simulation steps, tables stay intact; code, file paths, commands and data samples stay byte-exact. Compression applies to sentences only, never to substance.
1. **Recon**: tree + manifests → stack, entry points; multi-service → boundaries first, then read the single most critical flow end to end. Verify everything you'll assert.
2. **Scenario**: one concrete user action exercising the most of the system, including async paths (secondary color / noted separately).
3. **Screenshots**: run the app if possible (docker/npm scripts) and capture; else request from the user; else proceed without and say so.
4. **Build** in the chosen mode. 5. **Self-check** before delivering: Does the overview alone give a correct mental model? Does every code jump land on the implementing excerpt? Is every claim verifiable? Does any part read like a report? Fix, then deliver.
