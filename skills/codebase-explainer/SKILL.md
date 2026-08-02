---
name: codebase-explainer
description: Explain a codebase or flow as an interactive active-learning HTML page: step-through flows, predict-then-reveal quizzes, collapsible depth. For onboarding, "explain this code", "how does X work".
---

# Codebase Explainer

Goal: the reader ends up with **no gaps in their mental model** — they can picture the system running. That never happens through passive reading; the reader must walk the flow themselves, make predictions, get them wrong, and correct them. Three problems, three fixes:

1. **Content overload** → Feynman technique (plain language + one analogy + one traced real example)
2. **Presentation overload** → layered disclosure (overview always visible, detail on click)
3. **Passive reading** → active-learning mechanics (step-through player + predict-then-reveal questions)

## Core: Three Active-Learning Mechanics

Default output is a single-file interactive HTML page containing:

**1. Step-through flow player** — the most important flow (e.g. "what happens when the user clicks Buy") is NOT a static diagram. It's an animation the reader drives with Next/Back buttons. On each step: the active component is highlighted, completed components stay marked, and 1–2 sentences narrate exactly what happens at that step. The reader watches the request travel through the system with their own eyes — that's how the app comes alive in their head.

**2. Predict-then-reveal questions** — 2–4 questions, each posing a scenario about system behavior ("what happens if service X is down?"). The reader predicts first (clicks an option), then sees the correct answer plus the why. Questions must probe **behavior**, not trivia: not "which file?", but "what does the system do in this case?". Wrong options must be plausible — an obviously-wrong option teaches nothing.

**3. Layered depth** — no detail is deleted; it's folded:
- Layer 1 (always open): what this does + the big picture
- Layer 2 (opens on click): each flow/service/topic in its own section
- Layer 3 (folded inside Layer 2, "Technical details"): file names, edge cases, fragile spots, bugs found during analysis — as deep as needed

## Writing Rule: The Feynman Trio

For every explanation block:
1. **Plain language** — avoid jargon; if unavoidable, define it inline immediately
2. **One analogy** — from everyday life, and it must reflect how the mechanism actually behaves (message queue → a restaurant's order ticket rail; JWT → an event wristband; gateway → a building's front door)
3. **A named, concrete example** — not "the user" but "Alice"; not "a request" but "the 2 items in her cart". Abstract sentences like "this module manages X" are banned.

## Step 0: Mode and Format

- General request ("explain this project") → **Full Walkthrough**: player covers the most critical flow, quiz covers the whole system, each service gets a Layer 2 section
- Specific question ("how does X work") → **Focused Answer**: player for that flow only, 1–2 questions, single topic — do not expand scope
- Format: in chat contexts, **interactive HTML** (default). If the user wants a file saved into the repo, use **Markdown + `<details>` layers + mermaid** (GitHub/GitLab render these natively; the player and quiz don't exist in markdown — replace them with a sequence diagram plus "ask yourself" questions in plain text).

## Step 1: Recon — Read Only What You Need

- Directory structure + package manifest for the tech stack
- Find entry points (routes, main/index)
- Large/multi-service projects: first read only each service's entry point, then map the boundaries between services (HTTP? queue? shared DB?), then pick the 1–2 most critical flows and actually read them end to end
- **Verify every technical claim in the code with grep.** Every player step and every quiz answer must be grounded in real code. If you can't verify it, don't write it.
- In Focused Answer mode, scan only what the question touches

## Step 2: Build the HTML

Full working example: `references/interactive-example.html` — the skill applied to a sample 4-service project; contains the working code for the player, the quiz, and the layers. Use it as the template and swap in the project's content. Technical notes:

- Single file, vanilla JS — no frameworks, no CDN dependencies (player and quiz are pure JS/CSS; mermaid can be loaded from cdnjs if needed, but the player usually explains the main flow better than a diagram would)
- Layers use native `<details>/<summary>` (accessible, no JS)
- Player: components as a horizontal row of boxes; a `steps` array maps each step to the component it highlights + its narration text; Next/Back buttons + progress counter
- Quiz: one card per question; clicking an option marks the correct one green, the chosen wrong one red, and reveals the explanation
- Clean look: ~760px reading width, one accent color, no decoration

When writing player steps: one event per step; 4–8 steps is the sweet spot; narration must say **what happens and why**, not just "A calls B".

## Step 3: Diagrams (if needed)

The player already covers the main flow, so diagram needs drop. For an overall architecture picture or secondary flows, follow the type-selection guide in `references/diagram-guide.md`. Split any diagram exceeding 6–8 nodes.

## Step 4: Save / Present

- HTML → show via the chat's file-presentation mechanism; also save to disk if the user asks
- Markdown (into a repo) → small project: `docs/PROJECT_GUIDE.md`; large project: `docs/PROJECT_GUIDE.md` + `docs/services/<name>.md`; focused answer: `docs/qna/<topic>.md`
- Ask before overwriting an existing file

## Final Check

- Could someone who walked the player start-to-finish explain the flow to a colleague without help?
- Do quiz questions probe behavior rather than trivia? Are wrong options plausible?
- Does a reader who never clicks anything (Layer 1 only) still get the core idea? Does a reader who digs to Layer 3 find every detail?
- Is every claim verified in the code?
- Does any part read like a report? If so, rewrite it.

If any answer is "no", fix it before delivering.
