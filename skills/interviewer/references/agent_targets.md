# Coding-Assistant Rules-File Targets

This table exists so Phase 3 output generation never has to guess where a given coding
assistant loads its persistent instructions from. `AGENTS.md` is the canonical,
agent-agnostic deliverable — always generate it. Then, for each assistant the org
actually uses, generate the thin **pointer file** listed below alongside it. Never copy
the full Tier 1-3 content into a pointer file; that creates drift between two copies of
the same rules. A pointer file's only job is routing to the canonical file plus
assistant-specific mechanics that don't belong in an agent-agnostic doc.

| Assistant | Rules file(s) it reads natively | Pointer file to generate | Notes |
|---|---|---|---|
| Claude Code | `CLAUDE.md`, `AGENTS.md` (both auto-loaded) | `CLAUDE.md` | This repo's own root `CLAUDE.md` *is* the reference implementation of this pattern — read it before writing one. |
| OpenAI Codex CLI, Amp, and other `AGENTS.md`-native tools | `AGENTS.md` | none | These already treat `AGENTS.md` as their native format; a pointer file would be redundant. |
| Gemini CLI | `GEMINI.md` | `GEMINI.md` | Same 3-6 line pattern as `CLAUDE.md`: pointer + any Gemini-specific extension/tool notes. |
| GitHub Copilot (Workspace/Chat) | `.github/copilot-instructions.md` | `.github/copilot-instructions.md` | Copilot does not read arbitrary root markdown — the file must live at this exact path. |
| Cursor | `.cursor/rules/*.mdc` (current) or `.cursorrules` (legacy) | `.cursor/rules/agents.mdc`, or `.cursorrules` if the team is on an older Cursor version | Ask the user which convention their Cursor version uses if unsure — don't assume. |
| Windsurf | `.windsurfrules` | `.windsurfrules` | Same thin-pointer pattern. |
| Other / unnamed / proprietary | — | ask the user | Don't guess a tool's config convention. If the user doesn't know or the assistant isn't listed here, ship `AGENTS.md` alone and say so — most modern assistants fall back to reading it if nothing more specific is found. |

## Pointer file pattern

```markdown
# <ASSISTANT_NAME_HEADING>

This project's canonical agent instructions live in [AGENTS.md](./AGENTS.md) — read that first.
Everything below is <Assistant>-specific only; it does not replace or duplicate AGENTS.md.

## <Assistant>-specific notes
<only things unique to this assistant: hook/skill mechanisms, slash commands, tool-call
quirks, or "(none yet)" if there's nothing assistant-specific to add>
```

## Updating this table

If the user names an assistant not listed here, ask them where it reads instructions
from (or have them check its docs) rather than inventing a path — then it's fine to add
a new row to this file for next time.
