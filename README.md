# claude-audit

> A read-only auditor for your [Claude Code](https://claude.com/claude-code) environment.

Claude Code setups grow organically. Rules pile up, agents start to overlap, permissions get loose, and your context window quietly fills with content you forgot you wrote six months ago.

`claude-audit` takes a snapshot of your `~/.claude/` directory and your current project, then hands you back a structured report with a score out of 10 and concrete things to fix — sorted by priority.

No data leaves your machine. No file is ever modified.

## What it checks

| Area | What's audited |
|---|---|
| **Base structure** | `CLAUDE.md`, `settings.json`, memory system |
| **Rules & context weight** | What's loaded into every conversation, and how much |
| **Plugins, agents, commands & skills** | Relevance, overlap, duplication |
| **Security & hygiene** | Dangerous permissions, sensitive files, junk |
| **Quality & consistency** | Signal-to-noise ratio, MCP & hooks setup |

Each criterion is scored 0–3, then aggregated into a final `X.X / 10`. Recommendations come bucketed as **high / medium / low** priority, each one phrased as a concrete action — *"Move X to Y"*, *"Delete Z"*, not *"consider improving"*.

## Installation

```bash
mkdir -p ~/.claude/agents
curl -o ~/.claude/agents/claude-audit.md \
  https://raw.githubusercontent.com/mister-no-one/claude-audit/main/claude-audit.md
```

Or just drop `claude-audit.md` into `~/.claude/agents/` manually.

## Usage

In any Claude Code session, ask:

```
Run a full audit of my Claude Code environment.
```

Claude will dispatch the `claude-audit` agent automatically. The audit takes about 15–30 seconds and produces a single markdown report you can save, share, or act on directly.

## Example output

```markdown
# Claude Code Environment Audit

**Date:** 2026-04-07
**User:** jane.doe
**Audited project:** my-app

## Overall score: 7.3 / 10

*******---

## Summary

Solid setup with a clean memory system and well-scoped agents. Main issue:
context weight is high (~95 KB of rules loaded every conversation), several
of which are project-specific and should live in the project's CLAUDE.md.

## Critical issues
- `Bash(*)` wildcard found in settings.local.json
- 3 rules in ~/.claude/rules/ are specific to a single project

## Recommended improvements

### High priority
- Remove `Bash(*)` from settings.local.json, replace with scoped permissions
- Move ~/.claude/rules/project-conventions.md into the project's CLAUDE.md
...
```

## Guarantees

- **Read-only.** The agent has access to `Read`, `Glob`, `Grep` and `Bash`, but its instructions forbid any modification. You can verify this in `claude-audit.md`.
- **Local.** No network calls, no telemetry, no external services.
- **No secrets.** The audit explicitly skips `.credentials.json` and similar files.

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
