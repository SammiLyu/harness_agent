# Coding-Assistant CLI & Config Conventions

Helper lookup for filling `deployment_workflow_template.md`'s assistant-related
placeholders (`[AGENT_CONFIG_DIR_NAME]`, `[SSO_LOGIN_CMD]`, `[AGENT_HOME_VAR]`,
`[AGENT_MODEL_VAR]`, `[DISABLE_BROWSER_LOGIN]`) without making the user look up their own
tool's conventions. Treat every entry as a *starting point to confirm*, not a fact to
assert — CLI flags and env var names change between releases. If the user's version
disagrees with this table, trust the user and update the table for next time.

| Assistant | Config dir under `$HOME` | Typical interactive/SSO login | Notes |
|---|---|---|---|
| Claude Code | `.claude` | `claude` (prompts for login on first interactive run), or `claude auth login` | Model selection via `ANTHROPIC_MODEL` or `--model` flag, not a persistent env var by default. |
| Gemini CLI | `.gemini` | `gemini auth login` (opens browser OAuth) | Config also respects `GOOGLE_CLOUD_PROJECT` / `GOOGLE_CLOUD_LOCATION` for Vertex-backed setups. |
| GitHub Copilot CLI / Copilot in IDE | `.copilot` (CLI) or IDE-managed | GitHub account OAuth via `gh auth login` (CLI) or IDE sign-in — no separate Copilot-specific login step | Copilot Chat in an IDE doesn't have a standalone CLI login at all; skip the SSO step if the org only uses IDE Copilot. |
| Cursor | `.cursor` | IDE-integrated OAuth (no standalone CLI login) | Cursor is IDE-first; the "manual setup guide" framing may not fully apply — note this to the user rather than forcing CLI-style steps. |
| OpenAI Codex CLI | `.codex` | `codex login` (opens browser OAuth) or API-key env var fallback | This repo's security policy prohibits the API-key fallback (see Tier 1) — the guide must use the interactive login path. |
| Other / unnamed | ask the user | ask the user | Don't fabricate a login command for a tool not listed here. |

## How to use this during the deployment skill

1. Once you know which assistant(s) the deployment targets (from the interview profile,
   or by asking), look up the row(s) here first.
2. Only ask the user to confirm or correct values — don't ask them to supply from
   scratch what this table already has a good default for.
3. If a value here turns out to be stale (the user says "no, current syntax is X"),
   use the user's correction in the output and update this table so future runs of this
   skill get it right.
