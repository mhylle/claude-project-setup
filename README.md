# claude-project-setup

A reusable methodology for running software projects with Claude Code: a git-shared **memory bank**, a **progressive-disclosure development history**, **behavioral rules** that reduce common LLM mistakes, an optional **tasktracker MCP** integration as the planning system of record, and an optional **AI Layer** (per Anthropic's large-codebases article) for code-heavy repos.

Point a Claude Code session at this repo and it installs the methodology into your project — converting an existing project or bootstrapping a new one.

## Quick start — paste one of these into Claude Code

**Convert an existing project** (run from the project's root directory):

> Fetch https://raw.githubusercontent.com/mhylle/claude-project-setup/main/ADOPT.md and follow its instructions to convert this project to the methodology.

**Bootstrap a new project** (run from an empty or freshly created directory):

> Fetch https://raw.githubusercontent.com/mhylle/claude-project-setup/main/ADOPT.md and follow its instructions to bootstrap a new project named <NAME> — <one-line description>.

**Add the AI Layer to a code-heavy repo** (optional, after or independent of the above):

> Read AI-LAYER.md and the .claude/ folder in the Helpline repo (https://github.com/coleam00/helpline). It is a worked example of the AI Layer from Anthropic's large-codebases article. Build a comparable AI Layer for this codebase — a CLAUDE.md hierarchy, hooks, skills, an MCP, a subagent — adapted to our structure and conventions.

That's it. The session reads `ADOPT.md`, inspects your project, and does the rest — asking you only when a decision is genuinely yours to make.

## What gets installed

| File | Purpose |
|---|---|
| `CLAUDE.md` | Always-loaded router: behavioral rules, project overview, repo map, memory system, history procedure |
| `CLAUDE-activeContext.md` | Current state, goals, next-phase options — the authoritative "where we are" |
| `CLAUDE-history.md` + `history/` | Development history: lightweight index + one file per entry, `.counter` for sequence numbers |
| `.claude/rules/core-rules.md` | Memory-resilience rules |
| `.claude/commands/update-memory-bank.md` | `/update-memory-bank` slash command |
| `.claude/agents/memory-bank-synchronizer.md` | Agent that reconciles memory bank files against repo reality |

Plus, when a tasktracker MCP is available in the session: a tasktracker project registered as the planning system of record, and a CLAUDE.md section mandating the `tt-*` skills over file-based planning.

## Repo layout

| Path | Contents |
|---|---|
| `ADOPT.md` | The installer — step-by-step instructions a Claude Code session follows |
| `templates/` | The files that get installed, with `{{PLACEHOLDER}}` markers |
| `docs/methodology.md` | Why the system is shaped this way (for humans) |
| `docs/ai-layer.md` | The optional AI Layer phase for code-heavy repos |

## Origin

Combines [centminmod/my-claude-code-setup](https://github.com/centminmod/my-claude-code-setup)'s memory bank, Karpathy-style behavioral rules, a tasktracker MCP as planning system of record, and [coleam00/helpline](https://github.com/coleam00/helpline)'s AI Layer pattern from [Anthropic's article on Claude Code in large codebases](https://www.anthropic.com/engineering/claude-code-best-practices) — extracted and generalized from a working project setup.
