# agent-skills

A collection of Agent Skills for Claude (Claude.ai, Claude Code) and other agents supporting the [Agent Skills](https://agentskills.io) standard.

## Skills

| Skill | What it does | Install |
|---|---|---|
| [codebase-explainer](skills/codebase-explainer/) | Turns any codebase (or a question about one) into an interactive, active-learning explanation — step-through flow player, predict-then-reveal quizzes, collapsible technical depth | `npx skills add alialtunar/agent-skills@codebase-explainer` |

More skills coming.

## Install

**Via skills.sh (Claude Code, Cursor, etc.):**
```bash
npx skills add alialtunar/agent-skills@codebase-explainer
```

**Manually (Claude.ai):**

Settings → Capabilities → enable *Code execution and file creation*, then Customize → Skills → upload a zip of the skill folder (e.g. `skills/codebase-explainer/`).

**Manually (Claude Code):**

Copy a skill folder into `~/.claude/skills/` (personal) or `.claude/skills/` (project).

## Skill details

### codebase-explainer

Produces a single-file HTML page where the reader:

- **Steps through real request flows click-by-click** — watching the request travel between components, with per-step narration
- **Tests understanding with predict-then-reveal questions** — guess what the system does in a scenario, then see the answer and why
- **Expands collapsible layers for full technical depth** — file names, edge cases, fragile spots, and real bugs found during analysis, verified against the actual code

Writing follows the Feynman technique: plain language, one grounding analogy per concept, one traced concrete example. Falls back to Markdown with collapsible sections when saving docs into a repo.

Say things like: "Explain this codebase", "Onboard me to this project", "What happens when I click X?"

## Repository structure

```
skills/
  <skill-name>/
    SKILL.md          # the skill definition
    references/       # optional supporting files
```

## License

MIT
