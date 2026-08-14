# The Tiered Onboarding Framework (Modular Agent Bias Harness)

An open-source, modular, and customizable ruleset template system designed to govern and constrain AI Coding Assistants safely across diverse compute environments—ranging from shared high-performance compute (HPC) clusters to enterprise cloud VPCs and local containerized configurations.

This repository provides the modular templates, interactive onboarding interviewer, and provisioning tools necessary to establish **context-aware, tiered operational boundaries** for AI assistants on any software project.

---

## 0. What is an "Agent Harness" Here?

In this project, **"agent harness" does not refer to application code running an LLM loop.** 

Instead, it refers to the assembled, tiered set of markdown guideline files loaded into a coding assistant's context (such as an `AGENTS.md`, `CLAUDE.md`, or `.github/copilot-instructions.md` system prompt). This ruleset acts as a **behavioral governor (or bias harness)**—constraining what the assistant is permitted to do autonomously, what storage/compute quotas it must respect, what team-level code styles it must follow, and how its execution environment must be provisioned.

---

## 1. Core Architecture (The Tiered Bylaws)

The harness is assembled hierarchically, allowing organizations to maintain consistent safety rules while giving teams and projects the flexibility to specialize or override constraints.

```
       ┌────────────────────────────────────────────────────────┐
       │ Tier 1: Organization General (org_general_template.md) │  <-- Base Safety / Trust
       └───────────────────────────┬────────────────────────────┘
                                   ▼
       ┌────────────────────────────────────────────────────────┐
       │  Tier 2: Platform Environment (platform_template.md)   │  <-- Resource Queues / Quotas
       └───────────────────────────┬────────────────────────────┘
                                   ▼
       ┌────────────────────────────────────────────────────────┐
       │   Tier 3: Team Preferences (team_preferences_template) │  <-- Code Style / Git Boundaries
       └────────────────────────────────────────────────────────┘
```

*   **Tier 1 (Organization General)**: Baseline safety rules. Enforces dual-factor verification for file-system deletions, defines honesty/trust parameters (proof before completion assertions, capping infinite retry loops), and outlines strict sensitive data handling boundaries (e.g., HIPAA, GDPR, SOC2 compliance).
*   **Tier 2 (Platform Environment)**: Maps the physical environment. Segregates Head/Login nodes from Compute nodes, abstractly maps job submission syntax for schedulers (SLURM, Kubernetes, PBS, LSF), and redirects temporary directories or package managers to avoid filling restricted home directories (`$HOME`).
*   **Tier 3 (Team Preferences)**: Focuses on workspace isolation. Restricts Git command access to read-only research (`git status`, `git diff`, `git log`), defines code styles, and overrides platform paths for tighter project/group isolation.
*   **Provisioning & Deployment Guide** (`deployment_workflow_template.md`): An operational guide that walks administrators or developers through standing up proxy-aware, SSO-compliant coding assistant runtimes with symbolic links mapping persistence logs.

---

## 2. High-Value Use Case: One-Off Project Collaborations

One of the most powerful workflows enabled by this tiered structure is managing **one-off project collaborations** or spinning up ad-hoc project workspaces.

When a team spins up a new collaborative project, they don't have to write guidelines from scratch, nor do they have to settle for loose, generic coding assistant prompts that might leak sensitive data or crash shared clusters. 

### How It Works:
1.  **Inherit Global Rules**: The project naturally inherits Tier 1 (Organization Safety) and Tier 2 (Platform Quotas) from the global system.
2.  **Generate a Local Harness**: A collaborator invokes the framework's interviewer (either via the `interviewer` skill or using `templates/onboarding_interviewer.md` as their assistant's system prompt) inside the new project directory.
3.  **Bake In Local Overrides**: The interviewer prompts for project-specific overrides. For example:
    *   Surgical overrides for a custom, group-locked scratch folder under `/data/.../tmp/` to prevent write collisions.
    *   Specific repository mirrors or proxies needed only for this project's dependencies.
    *   Custom local testing or code quality checks.
4.  **Sit Contextually Alongside Global Configurations**: The generated rules compile directly into the project directory (e.g., as `./AGENTS.md` or a local subdirectory `src/GEMINI.md`). Modern AI assistants automatically pick up these local subdirectory configurations, ensuring they operate under surgical project-level bylaws while leaving the broader global rules untouched.

---

## 3. Quick Start (Using the Framework)

The repository provides two core automated workflows (implemented as Claude/Gemini skills in `.claude/skills/`):

### 1. Run the Onboarding Interviewer
To generate a tailored behavioral ruleset for an organization or a specific project workspace, use the **Interviewer Skill** or run the interviewer prompt.

*   **Prompt-based Execution**: Load `templates/onboarding_interviewer.md` as the system prompt for your AI assistant. It will guide you through:
    *   *Phase 1: High-Level Diagnostics* (Who are you, what compute platform option (A-D) do you use, how restrictive are your Git boundaries?).
    *   *Phase 2: Segmented Deep-Dive* (Interactive questions tailored precisely to your platform—skipping HPC details for cloud, and vice versa).
    *   *Phase 3: Output Generation* (Compiling answers into customized files under `output/`).
*   **Skill-based Execution** (if using a compatible terminal agent):
    ```bash
    # Run the interviewer skill
    @interviewer
    ```
    This generates an agent-agnostic canonical `output/AGENTS.md` ruleset, along with an `output/interview_profile.md` containing the diagnostic answers.

### 2. Generate the Provisioning Guide
Once the interview profile is generated, the **Deployment Skill** can consume it to create a fully-customized manual setup guide.

*   Run the deployment skill:
    ```bash
    @deployment
    ```
    This reads `output/interview_profile.md`, fills in the gaps from `templates/deployment_workflow_template.md` (injecting correct proxy settings, environment files, and SSO setup lines), and generates a fully-populated, placeholder-free `output/deployment.md` (the local `01_manual_setup.md` provisioning guide) as its own tier file. It also creates or updates `output/AGENTS.md`, the index file that points to every generated tier — Tier 1-3 plus this guide — and carries a placeholder section for the mandate/preference hierarchy across tiers.

### 3. The "One Core, Many Pointers" Pattern
To avoid duplicating large rulesets across multiple assistants, always use thin pointer files. Write your substantive rules once into each tier's own file (`org_general.md`, `platform.md`, `team_preferences.md`, `deployment.md`) — `output/AGENTS.md` itself stays a thin index pointing at those files, not a copy of their content — and create 3-line pointer files for each tool in use:

*   **For Claude Code (`CLAUDE.md`)**:
    ```markdown
    # CLAUDE.md
    This project's canonical agent instructions live in [AGENTS.md](./AGENTS.md) — read that first.
    ```
*   **For Gemini CLI (`GEMINI.md`)**:
    ```markdown
    # GEMINI.md
    This project's canonical agent instructions live in [AGENTS.md](./AGENTS.md) — read that first.
    ```

---

## 4. Contributor's Guide (Developing the Framework)

Welcome, contributor! This project is open-source, and we enforce a clean, modular architecture.

### Source of Truth vs. Generated Outputs
*   **`templates/` is the sole source of truth**. All template definitions, placeholders, and interactive prompts are defined here.
*   **Never hand-edit files inside `output/`**. Files in `output/` are compiled artifacts of the onboarding interviewer and deployment workflows and are gitignored. If you find a typo or want to refine a rule, modify its corresponding template under `templates/`.

### Zero Institutional Metadata Leakage Policy
This is a generic, open-source project shared publicly.
*   **No Private Identifiers**: Never commit specific corporate mirror URLs, private cloud project IDs, internal domain names, hostnames, or proprietary tool configurations.
*   **Use Placeholder Brackets**: Always use uppercase square brackets for configuration variables (e.g., `[ORGANIZATION_NAME]`, `[INTERNAL_PROXY_URL]`, `[SCHEDULER_TYPE]`).
*   **Run Pre-Commit Verification**: Before submitting your PR, scan your changes to ensure no local metadata has leaked:
    ```bash
    grep -rE "(your-internal-domain|your-local-cluster-name)" templates/
    ```

### Engineering Standards for Automation Scripts
If you are contributing scripts, test runners, or skills, you must adhere to our strict portable scripting guidelines:

1.  **Sourcing-Safe Shell Scripts**: Do not use global termination flags like `set -e`, `set -u`, or `set -o pipefail` in scripts designed to be sourced (e.g., profiles, environment setup blocks) or run in interactive terminal sessions. A single sub-command failure would immediately kill the developer's entire terminal session. Enforce safety with manual command-by-command checks:
    ```bash
    # Safe Pattern:
    command || { echo "Error: setup failed"; return 1; }
    ```
2.  **Safe Symlink Teardown**: Always use the dedicated `unlink` utility rather than `rm -f` when removing symbolic links to ensure safe detachment without risk of platform-specific recursive modifications to the targeted folder.
3.  **Dynamic Multi-User Sandbox Pathing**: Never write temporary files or installer downloads to static paths under `/tmp` (e.g. `/tmp/my-setup-dir`). This causes write-permission collisions in multi-user/HPC cluster environments if another user has already created that directory. Always use `mktemp -d` and trap cleanup on exit:
    ```bash
    TMP_DIR=$(mktemp -d -t setup_XXXXXX)
    trap 'rm -rf "$TMP_DIR"' EXIT
    ```
4.  **Terse Git Commit Messages**: We follow the `caveman-commit` standard: ultra-compressed, conventional commits subject (≤ 50 characters) focusing on "why" over "what".

### Extending Assistant Support
If a new coding assistant joins the market, help us add support:
*   Add a row mapping its behavior-loading convention under `.claude/skills/interviewer/references/agent_targets.md` (e.g. mapping whether it reads rules from `AGENTS.md`, `CLAUDE.md`, or a custom config directory).
*   Add its specific CLI setup commands, SSO configurations, and model environment variables under `.claude/skills/deployment/references/agent_cli_notes.md`.

---

## 5. Directory Layout Reference

*   `templates/`: The core customizable template layers (Org General, Platform, Team, Deployment) and the interactive interviewer prompt.
*   `.claude/skills/`: Custom skill logic (`interviewer`, `deployment`) and their respective target reference tables.
*   `context/`: Project-level session states, logs, and development progress tracking.
*   `AGENTS.md`: Master architectural specification,Terminology index, and long-term project roadmaps.
*   `output/`: (Gitignored) The compiled, tailored agent rule-harness and setup files.

---

## 6. License & Community

This project is licensed under the [GNU Affero General Public License v3 (AGPL-3.0)](LICENSE). Contributions, bug reports, and suggestions are highly encouraged! Please open an issue or submit a pull request following our contributor guidelines.
