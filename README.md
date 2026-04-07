# claude-healthcheck

> A read-only health check for your [Claude Code](https://claude.com/claude-code) configuration.

Like `brew doctor` or a Kubernetes healthcheck — but for your Claude Code setup.

Configurations grow organically. Rules pile up, agents start to overlap, permissions get loose, and your context window quietly fills with content you forgot you wrote six months ago.

`claude-healthcheck` takes a snapshot of your `~/.claude/` directory and your current project, then hands you back a structured health report with a score out of 10 and concrete things to fix — sorted by priority.

No data leaves your machine. No file is ever modified.

## What it checks

| Area | What's diagnosed |
|---|---|
| **Base structure** | `CLAUDE.md`, `settings.json`, memory system |
| **Rules & context weight** | What's loaded into every conversation, and how much |
| **Plugins, agents, commands & skills** | Relevance, overlap, duplication |
| **Security & hygiene** | Dangerous permissions, sensitive files, junk |
| **Quality & consistency** | Signal-to-noise ratio, MCP & hooks setup |
| **Integrity & cross-references** | Broken refs, dead permissions, stub commands, missing plugins, broken hooks |

Each criterion is scored 0–3, then aggregated into a final `X.X / 10`. Recommendations come bucketed as **high / medium / low** priority, each one phrased as a concrete action — *"Move X to Y"*, *"Delete Z"*, not *"consider improving"*.

## What makes it different

- **A real score on /10** — not just a list of warnings. You know if your setup is healthy at a glance.
- **Integrity checks** — finds broken slash commands, dead permissions, stub duplicates, hooks pointing to missing scripts. Goes beyond counting and linting.
- **Actionable, not preachy** — every recommendation is a concrete imperative ("Delete X", "Move Y to Z"), bucketed by priority.
- **One-click remediation** — the report ends with a copy-paste-ready prompt that walks through your high-priority fixes interactively.
- **Zero ceremony** — two `curl` commands and a `/healthcheck` slash command. No marketplace, no plugin registration.

## Installation

Two files: the agent (the brain) and the slash command (the trigger).

```bash
mkdir -p ~/.claude/agents ~/.claude/commands

curl -o ~/.claude/agents/claude-healthcheck.md \
  https://raw.githubusercontent.com/mister-no-one/claude-healthcheck/main/claude-healthcheck.md

curl -o ~/.claude/commands/healthcheck.md \
  https://raw.githubusercontent.com/mister-no-one/claude-healthcheck/main/commands/healthcheck.md
```

Or copy both files manually:
- `claude-healthcheck.md` → `~/.claude/agents/`
- `commands/healthcheck.md` → `~/.claude/commands/`

## Usage

In any Claude Code session, type:

```
/healthcheck
```

The check takes about 15–30 seconds and produces a single markdown report you can save, share, or act on directly.

## Example output

```text
# Claude Code Configuration Health Check

Date: 2026-04-07 — Score: 7.3 / 10  *******---
User: jane.doe — Project: my-app

## Scores by section

┌─────────────────────────────────────────┬───────┐
│                 Section                 │ Score │
├─────────────────────────────────────────┼───────┤
│ 1. Base structure                       │ 7/9   │
│ 2. Rules and context                    │ 9/12  │
│ 3. Plugins, agents, commands & skills   │ 9/12  │
│ 4. Security and hygiene                 │ 5/6   │
│ 5. Quality and consistency              │ 7/9   │
│ 6. Integrity & cross-references         │ 4/6   │
└─────────────────────────────────────────┴───────┘

## Critical issues
- `Bash(*)` wildcard found in settings.local.json
- /audit slash command references missing agent `claude-audit`
- 2 dead Bash() permissions referencing deleted paths

## Priority improvements

**High** (immediate impact):
- Remove `Bash(*)` from settings.local.json, replace with scoped permissions
- Delete ~/.claude/commands/audit.md (broken reference)
- Move ~/.claude/rules/project-conventions.md into the project's CLAUDE.md

## Next step — apply the fixes

To act on this report immediately, copy-paste the prompt below into Claude Code.
It will execute the high-priority fixes one by one and show you each diff before applying.

> Apply the high-priority fixes from the latest healthcheck report...
```

> 💡 Want a real screenshot? Run `/healthcheck` once and replace this code block with an image.

## Guarantees

- **Read-only.** The agent has access to `Read`, `Glob`, `Grep` and `Bash`, but its instructions forbid any modification. You can verify this in `claude-healthcheck.md`.
- **Local.** No network calls, no telemetry, no external services.
- **No secrets.** The check explicitly skips `.credentials.json` and similar files.

## Requirements

- Claude Code
- `bash` (default on macOS/Linux)
- `jq` *or* `python3` for JSON parsing

## Sharing it with your team

The report is designed to be shared. Run it on your own setup, paste the output in Slack, and watch your teammates run it on theirs — it surfaces inconsistencies across a team faster than a meeting would.

## Contributing

The scoring grid is intentionally opinionated. If your team has different standards, fork it and adapt — that's the whole point. Issues and PRs welcome.

## License

[MIT](./LICENSE) — © 2026 Grigori de Prada
