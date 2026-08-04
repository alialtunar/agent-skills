---
name: codebase-explainer
description: "Explain a codebase as a three-altitude interactive HTML guide: a concept overview, a user-driven system simulation with per-step data payloads, and annotated real code — with two-way jumps between simulation steps and their code. For onboarding, 'explain this code', 'how does X work'."
---

# Codebase Explainer

Goal: the reader ends up with a running mental model of the system — they can picture the machine working, and they can drill into the real code of any part without ever being buried in it.

The method is a **three-altitude ladder** in a single HTML file. Each altitude answers a different question, and the altitudes are cross-linked:

1. **🛰 Overview (2 min)** — *What problem does this solve, and what is the essence of the solution?* A few short paragraphs in plain language + one grounding analogy. No diagrams, no code. Ends with a button that drops the reader into the simulation.
2. **🚁 Simulation (5 min)** — *How does the machine run?* A spatial map of the real topology (nodes = actual components, labeled with real file names). The reader drives a real scenario step by step with Next/Back — an animated packet travels the edges, active components highlight, and two small cards update per step: a **two-line narration** (bold "what happens" + one muted "why it matters" line) and a **data payload panel** (max 3 lines: what is actually transmitted on that edge). Each step card has a "🔬 see this step's code" chip.
3. **🔬 Code (10 min)** — *What does the code actually say?* One section per simulation step: a short real excerpt (2–8 lines, gaps marked with a dim `# …`), inline comment annotations, and a paragraph on the design decision behind it. Each section ends with "↩ return to this step in the simulation", which restores the simulation at that exact step.

The two-way jumps are the core of the method: curiosity in the simulation is satisfied three seconds later at ground level, and ground level never loses the thread back to the running machine.

## Writing rules (apply at every altitude)

- **Feynman trio** per concept: plain language; one analogy that reflects the mechanism's real behavior; a named concrete scenario (a real user query traced end to end — reuse the same scenario across all three altitudes).
- **Code excerpts are short.** 2–8 lines per block, never a full function dump; elide with a dim `# …`. Real code only — copied from the repo, not paraphrased.
- **Ground truth or nothing.** Verify every claim with grep before writing it. Mine docstrings and code comments for design rationale — confessions of past bugs and "order matters" comments are gold; quote them.
- **Anchor to what the user sees.** If the project has a UI, map visible elements (tab names, counters, badges) to their code sources ("the 758/6000 bar on screen is this budget").
- **Calm visual design.** Light background, one accent color (+ one secondary for background/async flows), native fonts, ~760–880px reading width, no glow effects, no dark code panels at altitude 1. Short labels; numbered step tags ("3 · budget gate") so the reader always knows where they are.

## Workflow

**Step 1 — Recon.** Directory tree + package manifests → tech stack and entry points. For multi-service projects: entry points first, then boundaries (HTTP? queue? shared DB?), then read the single most critical flow end to end. Grep-verify everything you plan to assert.

**Step 2 — Pick the scenario.** One real, concrete user action that exercises the most of the system (ideally including any async/background paths, drawn in the secondary color). 7–10 simulation steps; one event per step.

**Step 3 — Build.** Start from `references/interactive-example.html` — it is a complete working implementation of the three-altitude engine (zoom bar, SVG map with animated packet, step/payload cards, code sections with flash-scroll jumps, back-links). Swap in the project's topology, scenario, payloads, and code excerpts. Keep it a single file, vanilla JS, no external dependencies.

**Step 4 — Self-check before delivering.**
- Does altitude 1 alone give a correct (if shallow) mental model?
- Can the reader who only drives the simulation explain the flow to a colleague?
- Does every "see code" jump land on the excerpt that actually implements that step?
- Is every quote/claim verifiable in the repo?
- Does any part read like a report? Rewrite it.

**Step 5 — Deliver.** Present the HTML in chat. If the user asks to save into the repo: `docs/` for the HTML; a Markdown fallback (same three sections, `<details>` for depth, mermaid sequence diagram replacing the simulation) if they need something GitHub renders natively.
