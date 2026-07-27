# AI Layer (optional phase for code-heavy repos)

The memory bank gives sessions *state*; the AI Layer gives them *navigation and guardrails* inside a substantial codebase. It's the pattern from Anthropic's article on using Claude Code in large codebases, with [coleam00/helpline](https://github.com/coleam00/helpline) as the worked example.

## When to run this phase

- The repo has (or is about to grow) a real codebase — multiple modules, more than a few thousand lines.
- Skip it for docs-only or trivial projects; the memory bank alone is enough there.
- Before deciding a stack is "not yet chosen" and deferring the components below on that basis, check the project's system of record (a tasktracker MCP project, ADRs, a docs file) — a stack/architecture decision can already be finalized there even though no code has been written in any repo yet. "No code exists" and "no stack decided" are different facts.
- You don't have to wait for code to exist. If the stack and architecture are already decided (in that system of record), the guardrail components — the CLAUDE.md hierarchy, hooks, and glob-scoped skills — are *most* valuable authored **before** implementation, because they steer it. The codebase-search MCP and per-directory CLAUDE.md then fill in as the code lands.

## The prompt

Paste this into a Claude Code session in the target repo:

> Read AI-LAYER.md and the .claude/ folder in the Helpline repo (https://github.com/coleam00/helpline). It is a worked example of the AI Layer from Anthropic's large-codebases article. Build a comparable AI Layer for this codebase — a CLAUDE.md hierarchy, hooks, skills, an MCP, a subagent — adapted to our structure and conventions.

## What it builds (adapt, don't copy)

| Component | Purpose |
|---|---|
| **CLAUDE.md hierarchy** | Lean root CLAUDE.md + per-directory CLAUDE.md files scoped to each module, loaded only when working there |
| **Hooks** | SessionStart hook (loads project context) and a self-improving Stop hook (captures learnings at session end). Make them **fail-safe** — a hook that throws can wedge a session, so never throw and always exit 0. |
| **Glob-scoped skills** | Skills that activate only for matching file patterns — framework rules, migration procedures, test conventions |
| **Codebase-search MCP** | AST-based structural search over the project's languages — cheaper and more accurate than grep for "find the definition/usages". A **stateless, rebuild-per-call** implementation (re-read the files each call) needs no index and never goes stale — a good default that even works before much code exists. It and a language server bottom out at the same engine, so if the tree grows large or goes polyglot you can re-back the same tools with an LSP-based engine without changing how the agent calls them. |
| **Explorer subagent** | Read-only subagent for codebase questions, keeping exploration out of the main context window |

## Interaction with the memory bank

The two are complementary and don't collide:

- If this repo already adopted the memory bank (`ADOPT.md`), the AI Layer's root CLAUDE.md content **merges into the existing CLAUDE.md** — don't create a second router. Behavioral rules, memory system, and history sections stay; the AI Layer adds the codebase hierarchy, hooks, and skill references.
- Directory-level CLAUDE.md files, hooks, skills, MCP config, and the subagent are all additive.
- If a tasktracker MCP is in use, wire it into the AI Layer's conventions (active-task discipline, requirement→task links) rather than adding a parallel tracking scheme.

## Practical notes

- **Sequence it guardrails-first.** The CLAUDE.md hierarchy, hooks, and skills encode conventions and are worth authoring up front so they steer the build; the codebase-search MCP indexes little on a near-empty repo but grows with it.
- **A newly added MCP needs a restart.** After registering a server in `.mcp.json`, install/build it and restart Claude Code before it's live — a freshly-registered MCP won't appear mid-session, which reads as "broken" if you don't expect it. Its dependencies must be installed too (e.g. an `npm install`, ideally auto-run via a `prepare` script), since build output is typically git-ignored.
