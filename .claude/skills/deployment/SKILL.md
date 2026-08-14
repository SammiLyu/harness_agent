---
name: deployment
description: >
  Generates a fully populated provisioning/setup guide from
  templates/deployment_workflow_template.md (this repo's Tiered Onboarding Framework's
  provisioning guide — package-manager & proxy setup, environment creation, SSO auth,
  config symlinking) and writes it to output/output.md. The guide stays assistant-agnostic in
  structure but is filled with the right per-assistant CLI/config details (looked up,
  not guessed) for whichever coding assistant(s) the org actually runs. Reuses org/
  platform/assistant details already captured by the interviewer skill when available,
  and only asks for whatever deployment-specific details are still missing. Use this
  whenever the user wants a setup guide, provisioning guide, deployment guide,
  "output.md", or instructions for standing up the coding-agent environment itself, or
  explicitly asks to run the deployment skill — even if they haven't run the interviewer
  skill first.
---

This project's canonical skill instructions live in
[skills/deployment/SKILL.md](../../../skills/deployment/SKILL.md) — read that first and
follow it in full, including its own `references/` files. Everything below is
Claude-Code-specific only; it does not replace or duplicate that file.

The `name` and `description` above are duplicated from the canonical file only because
Claude Code's skill discovery requires this exact path (`.claude/skills/<name>/SKILL.md`)
to list and trigger a skill. If you edit the skill's behavior, edit
`skills/deployment/SKILL.md` (and keep this frontmatter in sync) — don't add
orchestration logic here.

## Claude-Code-specific notes
_(none yet — this file exists so Claude Code's native skill-discovery convention has a
home; add Claude-Code-specific quirks here if/when this repo grows any, e.g. tool-access
restrictions or slash-command wiring unique to this skill.)_
