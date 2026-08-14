---
name: interviewer
description: >
  Interviews a sysadmin, DevOps engineer, or team lead to onboard their org/team onto
  this repo's Tiered Onboarding Framework, and compiles their answers into a filled
  agent-harness ruleset (Tiers 1-3: org safety, platform limits, team preferences).
  Produces an agent-agnostic canonical AGENTS.md plus thin, assistant-specific pointer
  files (CLAUDE.md, GEMINI.md, .github/copilot-instructions.md, etc.) for whichever
  coding assistants the org actually runs. Runs the three-phase protocol already defined
  in templates/onboarding_interviewer.md. Use this whenever the user wants to generate,
  onboard, set up, configure, or fill in an agent harness / coding-assistant ruleset for
  a real or test organization or team, or explicitly asks to run the onboarding
  interviewer or "the interviewer skill" — even if they only describe their org/platform
  casually rather than naming the framework.
---

This project's canonical skill instructions live in
[skills/interviewer/SKILL.md](../../../skills/interviewer/SKILL.md) — read that first and
follow it in full, including its own `references/` files. Everything below is
Claude-Code-specific only; it does not replace or duplicate that file.

The `name` and `description` above are duplicated from the canonical file only because
Claude Code's skill discovery requires this exact path (`.claude/skills/<name>/SKILL.md`)
to list and trigger a skill. If you edit the skill's behavior, edit
`skills/interviewer/SKILL.md` (and keep this frontmatter in sync) — don't add orchestration
logic here.

## Claude-Code-specific notes
_(none yet — this file exists so Claude Code's native skill-discovery convention has a
home; add Claude-Code-specific quirks here if/when this repo grows any, e.g. tool-access
restrictions or slash-command wiring unique to this skill.)_
