# CLAUDE.md — claude-skills

## Language

**All work must be done in English.**
- Commit messages, code comments, skill content, and documentation must be written in English.
- Korean is acceptable in conversation with the user, but all file content must be in English.

---

## Project Overview

This repository is a collection of Claude Code Superpowers skill plugins authored by [Kairyx-dev](https://github.com/Kairyx-dev). Each plugin contains one or more skills that extend Claude Code's behavior.

---

## Directory Structure

```
claude-skills/
├── .claude/
│   ├── CLAUDE.md                      # This file
│   └── .worktrees/                    # Git worktrees (gitignored)
│       └── <branch-name>/
├── .claude-plugin/
│   └── marketplace.json               # Plugin registry — lists all plugins in this repo
├── plugins/                           # One subdirectory per plugin
│   ├── <plugin-name>/
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json            # Plugin metadata (name, version, author)
│   │   ├── skills/
│   │   │   └── skill.md               # Skill content (frontmatter + instructions)
│   │   └── docs/                      # Plugin-level documentation (optional)
│   │       ├── decisions/             # MADR architecture decision records
│   │       │   └── NNNN-<title>.md
│   │       └── plans/                 # Implementation plans
│   │           └── YYYY-MM-DD-<topic>.md
├── docs/
│   └── superpowers/                   # Superpowers workflow docs (gitignored)
│       ├── specs/                     # Design specs from brainstorming
│       └── plans/                     # Implementation plans
├── README.md
└── .gitignore
```

### Key Files

| File | Purpose |
|------|---------|
| `.claude-plugin/marketplace.json` | Registers all plugins; consumed by the Superpowers plugin loader |
| `plugins/<name>/.claude-plugin/plugin.json` | Per-plugin metadata: name, version, description, author |
| `plugins/<name>/skills/skill.md` | Skill definition: YAML frontmatter + markdown instructions |

---

## Adding a New Plugin

1. Create `plugins/<plugin-name>/` directory
2. Add `.claude-plugin/plugin.json` with name, version, description, author
3. Add `skills/skill.md` with frontmatter (`name`, `description`, `user-invocable`) and instructions
4. Register the plugin in `.claude-plugin/marketplace.json`

### skill.md Frontmatter

```yaml
---
name: <skill-name>
description: Use when <trigger condition — written in English, this is what Claude reads to auto-invoke>
user-invocable: true
---
```

---

## Git Workflow

- Feature branches: `feat/<name>`, fix branches: `fix/<name>`
- Worktrees are created under `.claude/.worktrees/` (gitignored)
- Use `superpowers:using-git-worktrees` to create worktrees
- Use `git:clean` for the full PR lifecycle (commit → PR → review → merge → cleanup)
- Squash merge only

### Worktree Directory

Worktrees live at `.claude/.worktrees/<branch-name>`.
This path is gitignored via `.claude/.worktrees/**`.

---

## Codebase Exploration

**Use the Serena MCP server for all codebase exploration.**

- `mcp__serena__find_file` — locate files by name pattern
- `mcp__serena__find_symbol` — find symbols (functions, classes, keys) by name
- `mcp__serena__get_symbols_overview` — overview of symbols in a file
- `mcp__serena__search_for_pattern` — regex search across the codebase
- `mcp__serena__list_dir` — list directory contents

Prefer Serena tools over raw `find`/`grep` shell commands for code navigation.

---

## Commit Message Convention

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <description>

Types: feat, fix, docs, chore, refactor
Scope: plugin name (e.g., tidy-first, setup-agents) or omit for repo-level changes
```

Examples:
```
feat(tidy-first): add tidying checklist and language examples
fix(setup-agents): rename .cluade-plugin to .claude-plugin
docs: update README with tidy-first plugin
chore: add .claude/.worktrees to .gitignore
```
