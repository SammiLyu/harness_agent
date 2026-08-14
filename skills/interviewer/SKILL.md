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

# Interviewer Skill

You are running the onboarding interview for this repo's Tiered Onboarding Framework
(see `AGENTS.md` § 0 for what "agent harness" means here — it's the assembled markdown
rule tiers, not application code). The framework itself is deliberately agent-agnostic —
it's meant to configure *any* coding assistant a team uses, not just one — so nothing in
this skill should assume the org runs a particular assistant. Where an assistant-specific
detail is genuinely needed, look it up in `references/agent_targets.md` rather than
hardcoding one assistant's conventions into your instructions or your questions.

## Before asking anything

Read these files in full — they are the actual content of this skill, not just
reference material:

1. `templates/onboarding_interviewer.md` — this **is** your operating prompt. Adopt its
   persona and follow its Phase 1 → Phase 2 → Phase 3 protocol as written.
2. `templates/org_general_template.md`, `templates/platform_template.md`,
   `templates/team_preferences_template.md` — you need to know every `[PLACEHOLDER]`
   in these before you can ask the right Phase 2 questions.

Do not paraphrase the interview protocol from memory once you've read it — follow the
phases as written in `onboarding_interviewer.md`, since that file is the source of truth
and may be edited independently of this skill.

## Real deployment vs. test run

Per `AGENTS.md` § "Start Here": if it's unclear whether this is a real org/team
deployment or just a test of the generation flow, ask the user before proceeding. Either
way, write generated output into `output/` (gitignored) — never hand-edit files there
directly, and never write generated content back into `templates/` (that directory is
always the source of truth for the framework itself, not a place for one org's filled-in
values).

## Running the interview

Follow `onboarding_interviewer.md`'s three phases, with one addition to Phase 1: also
ask **which coding assistant(s) will actually load this harness** (Claude Code, Gemini
CLI, GitHub Copilot, Cursor, an `AGENTS.md`-native tool, something else, or "not sure
yet / multiple teams use different tools"). This determines which pointer files Phase 3
needs to produce — it doesn't change the Tier 1-3 content itself, since that content
stays assistant-neutral by design.

- **Phase 1**: the four profiling questions from `onboarding_interviewer.md`, plus the
  assistant question above.
- **Phase 2**: selective deep-dive scoped to the Phase 1 answers — skip whole sections
  that don't apply (e.g. skip HPC partition questions for a cloud/Kubernetes platform).
- **Phase 3**: compile and write the output files as described below.

This skill only covers Tiers 1-3 (behavioral rules). The deployment/provisioning guide
is a separate concern, handled entirely by the **deployment** skill — hand it off there
rather than trying to fill `deployment_workflow_template.md` yourself.

## Phase 3: agent-agnostic output, agent-specific pointers

1. Always generate the canonical, agent-agnostic file first: `output/AGENTS.md`,
   merging Tiers 1-3 per `onboarding_interviewer.md`'s Phase 3 instructions. If the user
   instead wants the segmented form (`org_general.md` / `platform.md` /
   `team_preferences.md`), generate those — either way, this is the one deliverable
   every assistant should be able to fall back to.
2. For each assistant named in the Phase 1 answer, open `references/agent_targets.md`,
   find its row, and generate the matching thin pointer file at the path that table
   specifies. A pointer file is a few lines: it defers to `AGENTS.md` for all actual
   rules and adds only what's mechanically specific to that assistant (hooks, skills,
   slash commands — whatever doesn't belong in an agent-agnostic doc). This repo's own
   root `CLAUDE.md` is a working example of exactly this pattern — look at it.
3. Never duplicate the full Tier 1-3 content into a pointer file. If you notice yourself
   copying more than a handful of lines into one, stop — that content belongs in
   `AGENTS.md`, and the pointer file should just link to it.
4. If the assistant isn't in `references/agent_targets.md` and the user doesn't know its
   config convention either, skip the pointer file and say so — `AGENTS.md` alone is a
   reasonable fallback for most modern assistants.

## Hand off to the deployment skill

In addition to the Phase 3 rules files, write `output/interview_profile.md`: a plain
summary of every answer the user gave during the interview (org name, compliance
standards, platform type and its specifics, team execution style, proxy/mirror URLs,
which coding assistant(s) are in use, package manager, environment name, storage paths,
etc.), labeled clearly by field name. This lets the **deployment** skill fill
`deployment_workflow_template.md` later without re-asking questions the user already
answered here — including which assistant(s) it should look up in its own
`references/agent_cli_notes.md`.

If the user mentions they already have previous tiered information — an existing
filled-out `org_general.md` / `platform.md` / `team_preferences.md`, a prior `AGENTS.md`,
or notes from an earlier onboarding round — don't silently merge or discard it. Record in
`output/interview_profile.md`, under its own clearly labeled note, exactly what the user
said they have (which tier(s) it covers, where it came from, and any values they gave
from it). That keeps the provenance of pre-existing values visible to anyone — including
the deployment skill — reading the profile later, instead of presenting reused values as
if they came from this interview.

Since `output/` is gitignored, real organizational details are fine to write there (this
is not the "no institutional metadata" restriction — that restriction is specifically
about the `templates/` folder, which must stay generic).

## Honesty

Per the Tier 1 template's own rules: never fabricate a placeholder value the user hasn't
given you. If something is unknown and the user has no answer, leave it as an explicit
`[UNKNOWN: ...]` note in the output and tell the user directly, rather than guessing.
