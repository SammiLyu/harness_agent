---
name: deployment
description: >
  Generates a fully populated provisioning/setup guide from
  templates/deployment_workflow_template.md (this repo's Tiered Onboarding Framework's
  provisioning guide — package-manager & proxy setup, environment creation, SSO auth,
  config symlinking) and writes it to its own tier file, output/deployment.md. Also
  creates or updates output/AGENTS.md, the index file that points to every generated
  tier (Tier 1 org_general.md, Tier 2 platform.md, Tier 3 team_preferences.md, and this
  guide's deployment.md) and carries a placeholder section for the mandate/preference
  hierarchy across tiers. The guide stays assistant-agnostic in structure but is filled
  with the right per-assistant CLI/config details (looked up, not guessed) for whichever
  coding assistant(s) the org actually runs. Reuses org/platform/assistant details
  already captured by the interviewer skill when available, and only asks for whatever
  deployment-specific details are still missing. Use this whenever the user wants a
  setup guide, provisioning guide, deployment guide, or instructions for standing up the
  coding-agent environment itself, or explicitly asks to run the deployment skill — even
  if they haven't run the interviewer skill first.
---

# Deployment Skill

You are generating this repo's Tiered Onboarding Framework's provisioning guide: the
manual setup guide that stands up the environment a harness will run in (see `AGENTS.md`
§ 0 — provisioning is operational, distinct from the behavioral rule tiers). The
guide's structure is assistant-agnostic — it walks through package manager, environment,
auth, and config-symlinking steps that apply regardless of which coding assistant is
being provisioned — but a few placeholders (login command, config directory name) are
genuinely assistant-specific. Look those up in `references/agent_cli_notes.md` rather
than asking the user to supply raw values they'd have to go look up themselves, and
rather than guessing.

This skill produces two kinds of output — the guide itself as its own tier file, and an
index that points to it alongside whatever other tiers already exist. See "Writing the
output" below for both.

## Before asking anything

1. Read `templates/deployment_workflow_template.md` in full — its structure, section
   order, and every `[PLACEHOLDER]` are what you must reproduce, filled in, in the
   output. Treat it as a fill-in-the-blanks form, not a springboard to freelance new
   sections.
2. Look for a prior interview profile to reuse before asking the user anything:
   - `output/interview_profile.md` (written by the **interviewer** skill), and
   - any other `output/*.md` files already generated (`org_general.md`, `platform.md`,
     `team_preferences.md`, `AGENTS.md`, or assistant-specific pointer files like
     `CLAUDE.md` or `GEMINI.md`).

   If any exist, read them and reuse every value they already establish (org name,
   platform type, proxy/mirror URLs, which coding assistant(s) are in use, package
   manager, storage paths, etc.) — do not re-ask the user for something already on
   record. In particular, reuse whichever assistant(s) `interview_profile.md` names, so
   you know which row(s) of `references/agent_cli_notes.md` apply.
3. If `output/AGENTS.md` already exists, read it too. It should normally be a thin index
   (see "The AGENTS.md index" below) — but if an earlier run left it as a single file
   with Tier 1-3 content merged directly into it (no separate `org_general.md` /
   `platform.md` / `team_preferences.md`), don't silently rewrite or split it. Tell the
   user it's in the merged form, that this skill's index format expects separate tier
   files, and ask whether to leave it alone or split it out before you proceed — don't
   guess.

## Filling in the gaps

`deployment_workflow_template.md` needs some placeholders an interview profile likely
won't have, since they're specific to provisioning rather than behavior — e.g. the
environment file name/format, dependency list, persistent-dir paths. For the
assistant-specific ones (`[AGENT_CONFIG_DIR_NAME]`, `[SSO_LOGIN_CMD]`,
`[AGENT_HOME_VAR]`, `[AGENT_MODEL_VAR]`, `[DISABLE_BROWSER_LOGIN]`), check
`references/agent_cli_notes.md` first — if the target assistant is listed there, use
that as your default and just ask the user to confirm or correct it rather than asking
them to produce it from scratch.

If the assistant isn't known yet (no interview profile, or the profile says "not sure /
multiple teams"), ask which assistant(s) this guide is for before proceeding — the whole
guide may need to be generated once per assistant if a team genuinely runs more than one.

Batch whatever questions remain into as few rounds as possible and offer multiple-choice
options where sensible (matching the tone `onboarding_interviewer.md` uses elsewhere in
this framework).

## Writing the output

### The guide, as its own tier file

- Never edit `templates/deployment_workflow_template.md` itself — it's the source of
  truth and stays generic/placeholder-based for every future run.
- Write the fully populated guide to `output/deployment.md` — its own file, a peer of
  `org_general.md` / `platform.md` / `team_preferences.md`, not merged into any other
  file.
- Before finishing, verify there are no leftover `[PLACEHOLDER]`-style brackets in
  `output/deployment.md` (a quick `grep -n '\[[A-Z_]*\]' output/deployment.md` works).
  Any match means either a question was missed or a value is still unknown — resolve it,
  don't ship it with brackets in place.
- If a value is genuinely unknown and the user can't supply it, don't fabricate one
  (per the Honesty rules in `org_general_template.md`) — leave an explicit
  `[UNKNOWN: reason]` marker and call it out to the user directly instead of silently
  guessing or leaving a bare template placeholder. This applies especially to
  `references/agent_cli_notes.md` lookups: that table is a starting point to confirm,
  not a guaranteed-current fact — if the user corrects a value, trust them and use their
  correction over the table's default.

### The AGENTS.md index

After writing `output/deployment.md`, create or update `output/AGENTS.md` so it stays an
**index**, not a merge: a short file that points at each tier's own file rather than
restating any tier's content inline. Never copy rule content from `org_general.md`,
`platform.md`, `team_preferences.md`, or `deployment.md` into `AGENTS.md` — if you catch
yourself doing that, stop and link instead (same "one core, many pointers" principle
`AGENTS.md` § 8 already applies to assistant pointer files, applied here to the index).

- If `output/AGENTS.md` doesn't exist yet, create it with:
  - A one- or two-line intro stating it's an index into the tier files below and does
    not restate their content.
  - A `## Tiers` section listing, for each of Tier 1 (`org_general.md`), Tier 2
    (`platform.md`), Tier 3 (`team_preferences.md`), and the provisioning guide
    (`deployment.md`): a link plus a one-line description of what that tier covers —
    but only for files that actually exist in `output/`. For any tier that hasn't been
    generated yet, list it as not yet generated (e.g. "*(not yet generated — run the
    interviewer skill)*") rather than linking to a file that doesn't exist or inventing
    a description of content you haven't seen.
  - A `## Mandate & Preference Hierarchy` section containing only a placeholder for
    now — e.g. "*(TBD — will define precedence when tiers' mandates or preferences
    conflict.)*" — since this skill doesn't have the information to fill it in. Leave it
    as a clearly marked stub rather than guessing at an ordering.
- If `output/AGENTS.md` already exists in this index form, update it in place: add or
  refresh the `deployment.md` entry under `## Tiers` (this skill just wrote or updated
  that file), leave other tiers' entries as they are unless those files also changed,
  and leave the `## Mandate & Preference Hierarchy` section untouched if it's already
  been filled in — don't overwrite a real hierarchy with the placeholder.
