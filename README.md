# claude-skills

A collection of Claude Code plugins and skills.

---

## Plugins

### [setup-agents](plugins/setup-agents/skills/skill.md)

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

See [setup-agents documentation](plugins/setup-agents/skills/skill.md) for details.

---

### [tidy-first](plugins/tidy-first/skills/skill.md)

Kent Beck의 "Tidy First?" 방법론을 구현한 rigid process 스킬.

모든 코드 수정 전 **Tidy Gate**를 통과하여 structural change와 behavioral change를 항상 분리한다. 명시적 호출(`/tidy-first`)과 코드 작업 시 자동 감지 둘 다 지원한다.

**Tidy Gate — 4단계 프로세스:**
```
[코드 수정 요청]
    ↓
Analyze  — behavioral change인가, structural change인가?
    ↓
Assess   — 3가지 질문으로 tidying 필요성 판단
    ↓ (필요한 경우)
Tidy     — 12개 체크리스트에서 structural tidying 수행
    ↓
Behavioral Change — 원래 요청 수행
```

**타이밍 프레임워크:** Never / Later / After / **First** (기본값)

**Tidying 체크리스트 (12개):**
- 가독성: Guard Clauses, Explaining Variable, Explaining Constant, Chunk Statements, Order by Reading Order
- 구조: Extract Helper, Inline Helper, Dead Code Removal, Move Declaration and Initialization Together
- 응집도: Cohesion Ordering, Normalize Symmetries, Explicit Parameters

**Usage:**
```
/tidy-first
/tidy-first <파일명>
/tidy-first <설명>
```

See [tidy-first documentation](plugins/tidy-first/skills/skill.md) for details.

---

## Installation

### Via Claude Code Superpowers

Install the plugin from the marketplace or add this repository as a plugin source.

### Manual

Copy the desired skill file from `plugins/<plugin-name>/skills/skill.md` into your Claude Code skills directory.

---

## Author

[Kairyx-dev](https://github.com/Kairyx-dev)
