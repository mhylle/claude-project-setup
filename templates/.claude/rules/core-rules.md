# Core Rules

## Destructive Command Discipline

- **Never run a command that discards uncommitted work unless the user asked for that specific command, in that specific moment** — `git reset --hard`, `git checkout -- <path>`, `git clean -fd`, `git stash drop/clear`, `git push --force`, `rm -rf` on anything you didn't create. See CLAUDE.md Behavioral Rule 5 for the full list and the non-destructive substitutes.
- **Run `git status` before any command that changes branch or tree state.** Dirty tracked files you did not create are the user's in-flight work — a stop sign, not an obstacle.
- To branch from a remote ref, use `git switch -c <name> origin/<branch>` — it preserves dirty files and refuses rather than destroying when it can't.
- Recorded because it actually happened (2026-07-27): a session ran `git checkout main && git reset --hard origin/main` purely to "start from a clean base", and permanently destroyed uncommitted edits to 9 tracked files — including the project's own CLAUDE.md. No reflog entry, no stash, no editor history. `git switch -c` would have done the intended job with zero loss.

## Memory Resilience

- When updating `CLAUDE-activeContext.md`, `CLAUDE-history.md`, or other memory bank files, also sync key new facts to native auto memory topic files
- After learning durable behavioral preferences from corrections, save them as auto memory entries AND add them to CLAUDE.md's "Durable Working Preferences" if project-wide
- If CLAUDE.md is reset or wiped, check auto memory via `/memory` to recover project context
