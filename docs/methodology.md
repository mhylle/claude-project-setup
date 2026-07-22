# The Methodology

Why the installed system is shaped the way it is. For humans; Claude only needs `ADOPT.md`.

## The problem

Claude Code sessions are stateless. Every cold start re-derives project state from scratch, misreads stale docs as current, and repeats mistakes the previous session already corrected. On multi-month projects this costs more than the coding itself.

## The pieces

### 1. Memory bank (git-shared)

Three files, committed to the repo so every machine and every session shares them:

- **`CLAUDE.md`** — the always-loaded router. Behavioral rules, project overview, repo map, and *pointers* to everything else. Kept under ~200 lines: it's loaded every session, so every line costs context. If removing a line wouldn't cause mistakes, it gets cut.
- **`CLAUDE-activeContext.md`** — the authoritative "where we are". Read at session start, updated at the end of significant sessions. The single most valuable file for cold starts.
- **`CLAUDE-history.md` + `history/`** — progressive disclosure. The index holds one table row per entry; full entries live in individual files, loaded on demand. Sessions read the index, not the archive.

### 2. Auto memory (machine-local shadow)

Claude Code's native auto memory mirrors the key facts. It's the recovery path if the git-shared files are ever wiped or reset — and it captures user preferences that outlive any one repo.

### 3. Behavioral rules (Karpathy-style)

Four rules at the top of CLAUDE.md — think before coding, simplicity first, surgical changes, goal-driven execution. They exist because these are the four most common LLM failure modes: silent assumptions, speculative over-engineering, drive-by refactoring, and unverifiable "done".

### 4. History discipline

Only meaningful state transitions get logged — finalized features, decisions, resolved investigations, milestones. Typo fixes and intermediate saves don't. The bar: would a future session act differently for knowing this?

### 5. Tasktracker as system of record (when available)

Markdown plans rot. When a tasktracker MCP is available, requirements, architecture, and phased plans live there, and CLAUDE.md mandates the tasktracker-native `tt-*` skills over file-based planning skills. The lesson behind this rule: on the originating project, a complete multi-phase implementation plan lived in tasktracker while the repo's own memory bank still said "implementation planning is the next phase" — two sources of truth had silently diverged. One system of record; the memory bank *points* at it.

### 6. AI Layer (code-heavy repos)

For substantial codebases, the memory bank isn't enough — sessions also need cheap, accurate ways to *navigate* the code. That's the AI Layer from Anthropic's large-codebases article: a CLAUDE.md hierarchy, hooks, glob-scoped skills, a codebase-search MCP, and a read-only explorer subagent. See `ai-layer.md`.

## The lifecycle

1. **Adopt** — point a session at `ADOPT.md` (convert or bootstrap).
2. **Work** — sessions read `CLAUDE-activeContext.md` at start, follow the behavioral rules, plan in tasktracker if present.
3. **Sync** — after significant work: update active context, add a history entry if warranted, mirror to auto memory (`/update-memory-bank` does all three).
4. **Reconcile** — when drift is suspected, run the `memory-bank-synchronizer` agent: it fixes factual staleness while preserving roadmaps, rationales, and preferences.
