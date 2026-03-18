# claude-skills

A collection of Claude Code plugins and skills.

---

## Skills

### [setup-agents](skills/setup-agents/skill.md)

Generates a complete multi-agent pipeline for any project.

Given a project description or directory path, it produces 4 specialized agents and 5 pipeline commands — all tailored to the project's actual language, architecture, and technology stack.

```
.claude/
├── agents/
│   ├── explorer.md    — read-only codebase analysis (Haiku)
│   ├── planner.md     — implementation planning (Opus)
│   ├── coder.md       — code generation (Sonnet)
│   └── validator.md   — test execution & fix (Sonnet)
└── commands/
    ├── explore.md     — /explore <task>
    ├── planning.md    — /planning <task>
    ├── code.md        — /code <task>
    ├── validate.md    — /validate <task>
    └── solve-task.md  — /solve-task <task>  ← full pipeline
```

**Usage:**
```
/setup-agents <project description or directory path>
```

See [setup-agents documentation](skills/setup-agents/skill.md) for details.

---

## Installation

### Via Claude Code Superpowers

Install the plugin from the marketplace or add this repository as a plugin source.

### Manual

Copy the desired skill file from `skills/<skill-name>/skill.md` into your Claude Code skills directory, and the corresponding file from `commands/` into `.claude/commands/`.

---

## Author

[Kairyx-dev](https://github.com/Kairyx-dev)
