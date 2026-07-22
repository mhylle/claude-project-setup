# ADOPT.md — Installer Instructions

You are a Claude Code session that has been asked to install this methodology into the project in your current working directory. Follow these steps in order. Inspect before you write, merge rather than overwrite, and ask the user only when a decision is genuinely theirs.

## Step 0 — Determine the mode

- **Convert:** the directory contains an existing project (code, docs, or git history).
- **Bootstrap:** the directory is empty or near-empty, and/or the user's prompt named a new project.

If it's ambiguous (e.g., a directory with only a LICENSE), ask.

For **bootstrap** mode with no project description in the prompt, ask the user for: project name, one-line description, and whether there's a GitHub remote to create or use.

## Step 1 — Get the templates

Fetch each raw file (public repo, no auth needed):

```
https://raw.githubusercontent.com/mhylle/claude-project-setup/main/templates/CLAUDE.md
https://raw.githubusercontent.com/mhylle/claude-project-setup/main/templates/CLAUDE-activeContext.md
https://raw.githubusercontent.com/mhylle/claude-project-setup/main/templates/CLAUDE-history.md
https://raw.githubusercontent.com/mhylle/claude-project-setup/main/templates/.claude/rules/core-rules.md
https://raw.githubusercontent.com/mhylle/claude-project-setup/main/templates/.claude/commands/update-memory-bank.md
https://raw.githubusercontent.com/mhylle/claude-project-setup/main/templates/.claude/agents/memory-bank-synchronizer.md
```

(Or, if fetching is unavailable, `git clone --depth 1 https://github.com/mhylle/claude-project-setup` into your scratchpad and read the files from there.)

## Step 2 — Inspect the project (convert mode)

Before filling any placeholder, learn the project:

1. Read any existing `CLAUDE.md`, `README.md`, and top-level docs.
2. Map the directory structure (top two levels is usually enough).
3. Skim `git log --oneline` (last ~30 commits) for phase/state signals.
4. Check for a remote: `git remote -v`.
5. Check whether a tasktracker MCP is available in this session (look for `tasktracker_*` tools), and if so, whether a project for this repo already exists (`tasktracker_listProjects`, search by repo/project name).

## Step 3 — Install the memory bank

Fill every `{{PLACEHOLDER}}` in the templates with real content from your inspection. **Never leave placeholder text or invented facts in an installed file.** If you can't determine a value, ask.

- `CLAUDE.md` → project root.
  - **If a CLAUDE.md already exists: merge.** Keep the user's existing content; add the template's sections (Behavioral Rules, Memory System, Development History Requirement, Rules Dependency) around it. Never silently delete existing instructions — if something conflicts, show the user both and ask.
  - Include the **Tasktracker (Mandatory)** section only if a tasktracker MCP is available (Step 5). Otherwise omit it entirely.
  - Review the template's "Durable Working Preferences" with the user if they differ from how this project has clearly been run.
- `CLAUDE-activeContext.md` → project root. In convert mode, seed "Where We Are" from git history and docs — this file must be true on day one, not empty scaffolding.
- `CLAUDE-history.md` → project root. Start the table empty; add a "Phase 0" summary line describing everything that pre-dates the history system (one paragraph, from git log).
- `history/` directory with a `.counter` file containing `1`.
- `.claude/rules/core-rules.md`, `.claude/commands/update-memory-bank.md`, `.claude/agents/memory-bank-synchronizer.md` → same paths in the project. If `.claude/` already has files, add alongside; merge only on filename collision, and show the user any conflict.

## Step 4 — Bootstrap extras (bootstrap mode only)

- `git init -b main` if not already a repo.
- Create a minimal `README.md` (name + one-line description) if none exists.
- If the user wants a GitHub remote: `gh repo create` (ask public/private), add remote.

## Step 5 — Tasktracker integration (only if the MCP is available)

1. If no tasktracker project exists for this repo, create one (`tasktracker_createProject`) with a real description of the project. If one exists, use it — never create a duplicate.
2. Add the **Tasktracker (Mandatory)** section to CLAUDE.md (marked in the template) with the actual project name and id filled in.
3. The rule this section encodes: tasktracker is the system of record for requirements, architecture, and phased plans. Use tasktracker-native skills (`tt-brainstorm`, `tt-create-plan`, `tt-implement-plan`, `tt-implement-phase`) instead of file-based equivalents (`brainstorm`, `create-plan`, `implement-plan`, `implement-phase`) whenever they're available.

If no tasktracker MCP is available, skip this step and remove the marked section from CLAUDE.md.

## Step 6 — AI Layer (optional, code-heavy repos)

If the project has a substantial codebase (or will soon), tell the user about the optional AI Layer phase and offer to run it — see `docs/ai-layer.md` in this repo. Its core prompt:

> Read AI-LAYER.md and the .claude/ folder in the Helpline repo (https://github.com/coleam00/helpline). It is a worked example of the AI Layer from Anthropic's large-codebases article. Build a comparable AI Layer for this codebase — a CLAUDE.md hierarchy, hooks, skills, an MCP, a subagent — adapted to our structure and conventions.

Don't run it unprompted for docs-only or trivial projects.

## Step 7 — First history entry, mirror to auto memory, commit

1. Create history entry `001` (category: `configuration`) recording the adoption — follow the procedure now in the installed CLAUDE.md.
2. If your session has native auto memory, mirror the key facts (project state, memory bank adoption, tasktracker project id if any) into it.
3. `git add` the installed files, review `git status` for anything unexpected, commit ("Adopt claude-project-setup methodology — memory bank, history system, core rules"), and push if a remote exists.

## Step 8 — Verify

Confirm each item; fix anything that fails before reporting done:

- [ ] `CLAUDE.md` exists, has no `{{...}}` left, and preserved all pre-existing user instructions
- [ ] `CLAUDE-activeContext.md` describes the project's *actual* current state
- [ ] `CLAUDE-history.md` + `history/.counter` (`2` after entry 001) are consistent
- [ ] `.claude/rules/`, `.claude/commands/`, `.claude/agents/` files installed
- [ ] Tasktracker section present **iff** the MCP is available, with a real project id
- [ ] Committed (and pushed, if remote)

Then give the user a short summary: what was installed, what was merged, what was skipped and why, and — in convert mode — anything in the existing project that contradicted its own docs (surface it, don't silently resolve it).
