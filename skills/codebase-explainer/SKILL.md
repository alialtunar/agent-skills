---
name: codebase-explainer
description: "Explain a codebase as an interactive three-altitude HTML guide (overview, driveable simulation, annotated code) or a screenshot-anchored Markdown guide. For onboarding and how-does-X-work questions."
---

# Codebase Explainer

Goal: the reader ends up with a running mental model of the system — they can picture the machine working, recognize it on their own screen, and drill into the real code of any part without being buried in it.

Four deliverable modes, same content standards — chosen with the person (Step 0):
- **Interactive Guide (HTML)** — three-altitude ladder, detailed below.
- **Project Guide (Markdown)** — lives in the repo (`docs/PROJECT_GUIDE.md`), renders natively on GitHub.
- **Code Tour (HTML)** — guided reading of the real source, stop by stop.
- **Q&A Explorer (HTML or MD)** — question-driven reference.

## Step 0 — Ask which kind of explanation (when the request is open-ended)

If the request is open-ended ("explain this project", "onboard me"), offer a short menu before building — one line per option, then respect the choice:

| Option | What it is | Best when |
|---|---|---|
| **Interactive Guide** | Three-altitude HTML: overview → driveable simulation → annotated code | First contact; "make it come alive" |
| **Project Guide (MD)** | Screenshot-anchored Markdown that lives in the repo | Team onboarding; durable documentation |
| **Code Tour** | Real files in reading order, line-by-line commentary | "I want to read the actual source" |
| **Q&A Explorer** | Real developer questions + short answers + code proof | Specific curiosities; troubleshooting mindset |
| **You pick** | Skill chooses based on the project and the request, states its reason in one sentence | Undecided |

Skip the menu when the request already implies a format: "write a guide into docs/" → Markdown mode; "how does X work?" → focused answer, no menu; "walk me through the code" → Code Tour. Never ask twice in one conversation — remember the first choice. If the person skips the menu or doesn't answer, default to **You pick** and say why in one sentence. Only ask a second question (whole project vs. one flow) when the scope is genuinely ambiguous; otherwise infer it.

**Code Tour mode** — sequential stops through the real files in comprehension order (entry point → core flow → the subtle parts). Each stop: the actual excerpt with highlighted lines; clicking a highlighted line reveals that line's story (why it exists, what breaks without it). Same content standards as everything else: short excerpts, grep-verified, docstring rationale quoted.

**Q&A Explorer mode** — 8–15 questions a developer would actually ask, grouped (entry, data flow, learning/state, failure modes, history). Each: one-line answer in bold, then the code proof and the deeper why in a collapsible block. Mine failure-mode questions from error handling paths ("what if X is down?").

## Content standards (both modes)

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

1. **Recon**: tree + manifests → stack, entry points; multi-service → boundaries first, then read the single most critical flow end to end. Verify everything you'll assert.
2. **Scenario**: one concrete user action exercising the most of the system, including async paths (secondary color / noted separately).
3. **Screenshots**: run the app if possible (docker/npm scripts) and capture; else request from the user; else proceed without and say so.
4. **Build** in the chosen mode. 5. **Self-check** before delivering: Does the overview alone give a correct mental model? Does every code jump land on the implementing excerpt? Is every claim verifiable? Does any part read like a report? Fix, then deliver.
