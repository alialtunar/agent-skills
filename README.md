# codebase-explainer

An Agent Skill that turns any codebase (or a question about one) into an **interactive, active-learning explanation** — not a passive report.

## What it produces

A single-file HTML page where the reader:

- **Steps through real request flows click-by-click** — watching the request travel between components, with per-step narration ("what exactly happens when the user clicks Buy")
- **Tests understanding with predict-then-reveal questions** — guess what the system does in a scenario, then see the answer and why
- **Expands collapsible layers for full technical depth** — file names, edge cases, fragile spots, and real bugs found during analysis, all verified against the actual code with grep (no hallucinated claims)

Writing follows the Feynman technique: plain language, one grounding analogy per concept, one traced concrete example. Falls back to Markdown with collapsible `<details>` sections when saving docs into a repo.

## Install

**Claude.ai:** Settings → Capabilities → enable *Code execution and file creation*, then Customize → Skills → upload the zip of `skills/codebase-explainer/`.

**Claude Code:** copy `skills/codebase-explainer/` into `~/.claude/skills/` (personal) or `.claude/skills/` (project).

## Use

Point Claude at a project and say things like:

- "Explain this codebase" / "Onboard me to this project"
- "What happens when I click X?" / "How does auth work here?"

## Structure

```
skills/codebase-explainer/
  SKILL.md                            # the skill
  references/
    interactive-example.html          # working example output (player + quiz + layers)
    diagram-guide.md                  # mermaid diagram type selection guide
```

## License

MIT
