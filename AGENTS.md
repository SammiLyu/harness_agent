# Project Agent Guidelines & Mission (AGENTS.md)

Welcome! You are an AI coding assistant operating inside this repository. This guide defines the master development policies, project goals, architectural conventions, and safety boundaries for the codebase.

To ensure success and seamless integration of your changes, you must strictly adhere to these guidelines.

---

## Start Here

This repo is the source for **The Tiered Onboarding Framework** — see § 0 for what "agent harness"
means here. Depending on what you were asked to do, go to one of these first:

1. **Extend or edit the framework itself** (add a new tier, fix a template, update the roadmap):
   edit the files under `templates/` — these are the source of truth (see § 2, § 7).
2. **Generate a harness for a real org/team**: run `templates/onboarding_interviewer.md` as your
   operating prompt — it interviews the user and fills the templates into output files.
3. **Test that generation works** (no real org involved): same as above, but treat the profile as a
   sample/test — write results into the gitignored `output/` directory, never hand-edit files there
   directly (they're generated, not source).

`output/` is generated and gitignored — never a source of truth. `templates/` is always the source
of truth. If unsure which of the three you're doing, ask the user before proceeding.

---

## 0. Terminology: What "Agent Harness" Means In This Project

In this repository, **"agent harness" does not mean application code that runs an LLM loop.**
It means the assembled, tiered set of markdown guideline files in `templates/` themselves.
Once populated for a specific organization/platform/team and loaded into a coding assistant's
context — as its system prompt, `AGENTS.md`, `CLAUDE.md`, or equivalent — these tiers *are* the
harness: they are what constrains and governs how the assistant is permitted to operate (what it
may do autonomously, what compute/storage boundaries it must respect, what team conventions it
must follow, and how its environment must be provisioned).

The harness is assembled per-deployment, tier by tier:
1. **Tier 1** (`org_general_template.md`) is the base layer — global safety, trust, and compliance
   rules that apply everywhere.
2. **Tier 2** (`platform_template.md`) extends Tier 1 with the concrete compute/storage environment.
3. **Tier 3** (`team_preferences_template.md`) narrows or overrides Tiers 1–2 for one team's conventions.
4. **The provisioning guide** (`deployment_workflow_template.md`, generated via
   `skills/deployment/SKILL.md`) is operational provisioning, not a behavioral
   rule tier, used to stand up the environment the harness will run in.

`onboarding_interviewer.md` is the prompt that interviews an administrator and assembles the filled
tiers into the final harness file(s) — either one consolidated rules file, or the segmented per-tier
files described in its Phase 3 — which is what actually gets loaded by the target coding assistant.

---

## 1. Project Goal & Core Mission

### The Goal
The primary goal of this project is to maintain and develop **The Tiered Onboarding Framework**. This framework is an open-source, modular, and customizable ruleset template system that enables software engineering teams, system administrators, and organizations to quickly configure and deploy secure, interactive, and platform-compliant coding assistant harnesses **— where "harness" refers to this tiered markdown ruleset itself (see § 0), not a separate software runtime.**

### The Problem Solved
Coding assistants must operate safely across diverse environments—from physical high-performance compute (HPC) clusters to enterprise cloud VPCs and Kubernetes federations. This project provides a hierarchical set of template bylaws that establish:
1.  **Tier 1 (Organization General)**: Core safety, interaction trust, data security, and compliance constitutions.
2.  **Tier 2 (Platform Environments)**: Resource default queues, nodes, filesystems, and repository mirrors.
3.  **Tier 3 (Team Preferences)**: Language styles, Git read-only boundary controls, and explicit platform overrides for team isolation.
4.  **Deployment Workflows** (see `skills/deployment/SKILL.md`): Secure, proxy-aware manual provisioning guides with interactive SSO authentication.
5.  **Master Interviewer Prompt**: An interactive setup prompter that empty agents can run to interview human administrators and generate filled, personalized rules files.

---

## 2. Tiered Framework Technical Specifications

Each tier below is one layer of the assembled agent harness defined in § 0.

When editing or extending the templates inside the `templates/` directory, ensure they satisfy these architectural specifications:

### Tier 1: Organization General (`org_general_template.md`)
-   **Interactive Safety**: Must enforce strict dual-factor validation for file-system deletions and recursive commands.
-   **Honesty & Trust Relationship**: Mandates explicit reporting of failures, skipped steps, and uncertainties. No model hallucinations or unverified completion claims.
-   **Sensitive Data Boundaries**: Establishes customizable compliance rules (e.g. HIPAA, PII, GDPR, SOC2) and prohibits hardcoding or committing credentials.

### Tier 2: Compute Environment (`platform_template.md`)
-   **Node Segregation**: Clearly separates interactive/login nodes from heavy compute environments.
-   **Scheduler Resource Mapping**: Provides abstract submission configurations for various schedulers (e.g., SLURM, Kubernetes, PBS, LSF) covering CPUs, memory, and wall-time limits.
-   **Filesystem Quotas**: Maps home directory limitations and enforces cache/temp redirection rules off restricted system blocks.

### Tier 3: Team Preferences & Overrides (`team_preferences_template.md`)
-   **Git Restrictions**: Establishes strict read-only research permissions (`git status`, `git diff`, `git log`) and forbids automatic staging/committing.
-   **Platform Override Mechanism**: Contains explicit configuration blocks allowing teams to override or isolate temporary directories (e.g. forcing group-locked scratch paths) instead of using globally shared platform temp directories.
-   **Technical Idioms**: Outlines sourcing-safe scripting, dynamic sandboxing (`mktemp`), safe symlink cleanup (`unlink`), and Conventional Commit conventions.

### Environment Provisioning (`deployment_workflow_template.md`, see `skills/deployment/SKILL.md`)
-   **Proxy & Mirror Setup**: Instructs on configuring environments behind enterprise firewalls using local proxy mirrors.
-   **SSO Compliance**: Detail-oriented walkthrough for setting up native interactive browser/auth-code SSO logins to completely prevent raw API key fallbacks.
-   **Quota Preservation**: Configures symbolic links from group storage directories back to the home directory for persistent rules and history storage.

### Master Interviewer Prompter (`onboarding_interviewer.md`)
-   **Profiling Diagnostic**: Guides an empty agent to first profile the organization safety scale, compute platform type, and proxy requirements.
-   **Selective Interviewing**: Walking the user dynamically only through the template tiers that match their specific profile (skipping irrelevant cluster blocks for cloud users).
-   **Generation Output**: Automatically compiles and outputs the final files based on the responses.

### Agent Interaction & Output Requirements
-   **Pre-Generation Verification**: The agent must explicitly request the organization's exact name and any applicable compliance or data-security requirements (e.g. HIPAA, GDPR, SOC2) from the user before generating any files or applying template values.
-   **Organization-Specific Outputs**: Files such as `org_general.md` must be populated with organization-specific, user-provided values. Do not emit the org file as a generic template with placeholders or hallucinated claims about the organization.
-   **Output Directory**: All generated files must be written to a single directory named `Output/` at the repository root (or a clearly named alternative chosen and confirmed by the user). The agent should list the generated files and their paths when finished.

---

## 3. Future Roadmap & Upcoming Milestones

Any future development and agent workflows on this project should focus on the following key milestones:

### Milestone 1: Expanding Platform Environments (Tier 2 Expansion)
-   Extend `platform_template.md` to provide pre-configured blocks for modern serverless architectures (AWS Fargate, Google Cloud Run) and standard Docker-compose developer setups.

### Milestone 2: Multi-Language Package Manager Support (Deployment Guide Expansion — see `skills/deployment/SKILL.md`)
-   Extend `deployment_workflow_template.md` to support seamless proxy-aware configurations across multiple ecosystem tools (Poetry, pipenv, Cargo, NPM, Maven, and Go Modules).

### Milestone 3: Dynamic Onboarding Interviewer Enhancements
-   Optimize `onboarding_interviewer.md` for different LLM provider styles (Claude, Gemini, GPT-4), ensuring robust parsing and prompt extraction.

### Milestone 4: Automated Output Validation Framework
-   Develop a lightweight test script (e.g. bash or python) that automates the verification of generated outputs. It should load the templates, mock the interviewer's output, and assert that the generated rules files contain valid Markdown/YAML structures with no empty placeholder brackets.

---

## 4. No Institutional Metadata Leakage
This is a generic, open-source project intended to be shared publicly across various organizations.
-   **Strict Generic Standard**: Never commit any specific institutional metadata, corporate mirror URLs, private cloud project IDs, private domains, or proprietary hostnames to the `templates/` folder or the documentation.
-   **Placeholder Rule**: Always utilize uppercase square brackets for configuration variables, e.g. `[ORGANIZATION_NAME]`, `[INTERNAL_PROXY_URL]`, or `[TEMP_DIR_DEFAULT]`.
-   **Pre-Commit Verification**: Before completing any modifications or additions to the templates, run a verification grep to scan for any unauthorized proprietary keywords specific to your local installation:
    ```bash
    # Scan for your local organization-specific sensitive keywords:
    grep -rE "([LOCAL_PROPRIETARY_KEYWORD_1]|[LOCAL_PROPRIETARY_KEYWORD_2])" templates/
    ```

---

## 5. Strict Git & Workspace Boundaries
-   **No Autonomous Staging/Commits**: You are strictly forbidden from automatically executing any Git commands that modify the index, repository history, or workspace state (such as `git add`, `git stage`, `git commit`, `git push`, or `git checkout`).
-   **Manual Verification Block**: Always present the precise Git commands as copy-pasteable blocks in your chat response for the developer to review and execute themselves.
-   **No Autonomous Recovery**: If a change was staged in error, stop immediately, report the status, and present the manual recovery commands (e.g. `git restore --staged <file>`). Do not attempt to fix or reset files autonomously.
-   **Authorized Read-Only Research**: You are fully permitted to run `git status`, `git diff`, and `git log` to research context.

---

## 6. Storage & Safe Scripting Conventions
-   **No Sourced Exit Flags**: Never use global safety flags like `set -e` or `set -u` in scripts intended to be sourced (e.g., shell profiles or environment scripts). Sourcing a script with these active will immediately kill the active developer terminal session upon any sub-command returning non-zero. Use manual conditional checks instead.
-   **Safe Symlink Teardown**: Always use `unlink` rather than `rm -f` when removing symbolic links to prevent any risk of modifying or recursively deleting contents inside the underlying target directory.
-   **Dynamic Sandboxing**: Avoid hardcoded sandbox directories (e.g. `/tmp/run`). Generate unique sandbox folders dynamically using `mktemp -d` and trap their cleanups on exit.

---

## 7. Directory Layout Reference
-   **`templates/`**: Core modular templates representing the Tiered Onboarding Framework (Org safety, Platform limits, Team overrides, and Provisioning guides) — **assembled together, these ARE the agent harness (see § 0)**, not the source for one.
-   **`context/`**: Active session history tracking and state logs (e.g., `context/SESSION_STATE.md`).
-   **`skills/`**: The agent-agnostic, canonical skill implementations (`interviewer`, `deployment`) that orchestrate the templates. This is the source of truth for what each skill does — tool-neutral, following the maintenance style in § 8. They produce agent-agnostic output (`AGENTS.md`, `output.md`) plus thin assistant-specific pointers/lookup tables, never assistant-specific rules baked into the output itself.
-   **`.claude/skills/`**: Thin, Claude-Code-specific pointer files — one per skill — that exist only because Claude Code's skill discovery requires a `.claude/skills/<name>/SKILL.md` file at that exact path. Each one defers to the matching file under `skills/` for all actual logic, mirroring root `CLAUDE.md`'s relationship to `AGENTS.md`. A future coding assistant with its own skill/extension convention gets the same kind of thin pointer alongside this one, never a duplicated copy of the orchestration logic.
-   **`AGENTS.md`**: This master guidelines file.

---

## 8. Agent-Agnostic Maintenance Style: One Core, Many Pointers

This repo's own root `CLAUDE.md` demonstrates the standard this project follows whenever
it produces or maintains files for a specific coding assistant: keep the substantive
content in one agent-agnostic core, and confine anything assistant-specific to a thin,
separately-scoped pointer.

-   **The core stays generic.** `AGENTS.md` (this file, and the compiled Tier 1-3 output
    the interviewer skill generates) is written for "an AI coding assistant" in the
    abstract — never for one product by name. The same applies to everything in
    `templates/`: none of the four templates should assume a specific assistant's config
    format, CLI, or hook system.
-   **Assistant-specific detail goes in a pointer or a lookup table, never inline.** A
    pointer file (like root `CLAUDE.md`, or a generated `GEMINI.md`) is a few lines
    deferring to the agent-agnostic core plus whatever mechanics are genuinely unique to
    that assistant. A lookup table (like
    `skills/interviewer/references/agent_targets.md` or
    `skills/deployment/references/agent_cli_notes.md`) captures per-assistant
    facts — where it reads its rules from, its login command — so that fact only has to
    be looked up once, not re-derived or hardcoded into orchestration logic.
-   **Never duplicate the core into a pointer.** If a pointer file starts accumulating
    actual rules content instead of routing back to the core, that's a sign the content
    belongs in the core and the pointer should shrink back down to a reference.
-   **The skill implementations follow their own rule, one level down.** `skills/` holds
    the canonical, tool-neutral orchestration logic; `.claude/skills/` holds nothing but
    thin per-tool pointer files into it (see § 7). This is the exact same core/pointer
    split applied recursively — the skills that generate agent-agnostic output are
    themselves stored in an agent-agnostic location, with only Claude Code's required
    discovery path kept as a pointer.

Apply this same split to any future template, skill, or automation this project adds
that touches more than one coding assistant.
