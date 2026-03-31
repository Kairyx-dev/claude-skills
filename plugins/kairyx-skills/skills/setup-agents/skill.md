---
name: setup-agents
description: Use when the user invokes /setup-agents or asks to generate/setup/create agents and commands for a project. Generates explorer, planner, coder, and validator agents plus 5 pipeline commands tailored to the project's stack.
---

## Usage
```
/setup-agents <project description or directory path>
```

## Description
Generates a complete set of sub-agents and commands for a new project.
Produces 4 agent files and 5 command files tailored to the project's
language, architecture, and technology stack.

All output files are written directly to `.claude/agents/` and `.claude/commands/`.

## Output Files

```
.claude/
├── agents/
│   ├── explorer.md
│   ├── planner.md
│   ├── coder.md
│   └── validator.md
└── commands/
    ├── explore.md
    ├── planning.md
    ├── code.md
    ├── validate.md
    └── solve-task.md
```

---

## Instructions

You are generating a complete agent and command setup for a new project.
The project description and/or root directory path is provided in $ARGUMENTS.

---

### Phase 1: Information Gathering

Before generating any files, collect all required information using the following steps.

#### Step 1: Auto-analyze the project directory

If $ARGUMENTS contains a directory path, analyze the project automatically:

1. List the directory tree to identify all modules and their structure
2. For each module, read the following files if they exist to infer responsibilities and stack:
   - `build.gradle`, `build.gradle.kts`, `pom.xml` — dependencies, plugins, language version
   - `settings.gradle`, `settings.gradle.kts` — module names
   - Source files under `src/main` — architecture patterns, annotations, frameworks in use
   - README or any `.md` files at the root — project description
3. From the above, infer as many items as possible from the required information list below.

#### Step 2: Fill remaining gaps

After auto-analysis, identify any items that could not be confidently inferred.
Ask the user only about those remaining items — do not ask about anything already determined.
Group all remaining questions into a single interaction.

**Required information:**

| Item | Description |
|------|-------------|
| Language | Primary programming language (e.g. Kotlin, Java, TypeScript) |
| Platform | Target platform (e.g. Android, Spring Boot, Node.js) |
| Language version | e.g. JDK 1.8, JDK 25, Kotlin 1.9 |
| Architecture | e.g. Clean Architecture, Hexagonal, MVC |
| Module/directory structure | Full list of modules and their responsibilities |
| UI framework | e.g. Jetpack Compose, XML View, React, None |
| Async/concurrency model | e.g. Coroutines + Flow, RxJava, CompletableFuture, none |
| DB access technology | e.g. Room, MyBatis, JPA, none |
| HTTP communication | e.g. Retrofit2 + Sandwich, RestTemplate, Axios, none |
| Error handling approach | e.g. sealed class, custom Exception hierarchy, Result type |
| Object mapping library | e.g. Mappie, MapStruct, manual, none |
| DI framework | e.g. Hilt, Spring DI, none |
| Logging library | e.g. Timber, SLF4J, console.log |
| Test framework | e.g. JUnit5 + Mockito, JUnit4 + MockK, Jest |
| Test code generation | Always / Never / Follow plan |
| Coder file behavior | Write files directly / Output only / Confirm before write |
| Planner pause | Pause pipeline at planner for user review? Yes / No |

If no directory path is provided, extract what is available from $ARGUMENTS
and ask the user about all missing items in a single interaction.

---

### Phase 2: File Generation

Once all required information is collected, generate the following 9 files.
Write each file directly to the paths listed below.

For each file, apply the project-specific information collected in Phase 1.
Do not use placeholder text — all content must reflect the actual project.

---

#### File 1: `.claude/agents/explorer.md`

Frontmatter:
- `name: explorer`
- `model: claude-haiku-4-5-20251001` — exploration is read-only and does not require the most powerful model
- `description:` one paragraph describing what the explorer does for this specific project, including architecture style and key technical components
- `tools: [read_file, list_directory, search_files, grep, bash]`

Sections to include:
- **Role**: exploration-only, no code changes, produces Explorer Report for planner
- **Project Context**: language, platform, architecture, full module structure table, DI framework, DB access, key libraries
- **Exploration Strategy**:
  - Step 1: Analyze the task (extract keywords, identify affected layer and module)
  - Step 2: Locate entry points (provide task-type-specific exploration sequences based on the project's actual architecture and features)
  - Step 3: Map dependencies (imports, DI bindings, build files)
  - Step 4: Collect patterns (async model, error handling, object mapping, logging, any project-specific patterns)
- **Output Format**: Explorer Report with sections — Task Summary, Related Modules, Key File List, Data Flow, Existing Patterns, DI Dependencies, Object Mapper Coverage, Cautions, Recommendations for Planner
- **Context handoff rules**:
  - The Explorer Report is the sole context passed to the planner — it must be self-contained
  - Every section must be filled; omit nothing even if the answer is "none found"
  - Do not summarize file contents — quote exact file paths, class names, and annotation names so the planner can reference them without re-exploring
- **Constraints**: no file modification, no implementation suggestions, mark uncertainty as "needs further exploration"

---

#### File 2: `.claude/agents/planner.md`

Frontmatter:
- `name: planner`
- `model: claude-opus-4-6` — planning requires deep reasoning over Explorer Report context
- `description:` one paragraph describing what the planner does for this specific project
- `tools: [read_file, list_directory, search_files, grep, bash]`

Sections to include:
- **Role**: planning-only, no code changes, produces Planner Report for coder
- **Project Context**: same module structure and stack as explorer
- **Input**: description of expected Explorer Report contents; instruct to request it if missing
- **Planning Strategy**:
  - Step 1: Validate Explorer Report completeness
  - Step 2: Categorize changes (New / Modify / No change)
  - Step 3: Order implementation steps — define the correct dependency-safe order for this architecture (e.g. domain-first for Clean Architecture, domain → outport → inport → usecase → adapter for Hexagonal)
  - Step 4: Identify risks per step
  - Step 5: Define acceptance criteria for validator
- **Output Format**: Planner Report with sections — Task Summary, Scope Overview, Implementation Plan (ordered steps with file, action, description, dependencies, pattern reference), DI/Mapper Updates, Risk Assessment, Acceptance Criteria, Notes for Coder
- **Context handoff rules**:
  - The Planner Report is the sole context passed to the coder — it must be fully self-contained
  - Each implementation step must include the exact file path, action type, and a description precise enough that the coder needs no further exploration
  - Acceptance Criteria must be written as objectively verifiable conditions — no vague statements like "works correctly"
  - If the Explorer Report is ambiguous on any point, mark the step as "NEEDS CLARIFICATION" rather than guessing
- **Constraints**: no code or pseudocode, mark ambiguous steps as "NEEDS CLARIFICATION", do not break architecture dependency direction

---

#### File 3: `.claude/agents/coder.md`

Frontmatter:
- `name: coder`
- `model: claude-sonnet-4-6` — code generation requires precise execution; planner has already resolved all architectural decisions
- `description:` one paragraph describing what the coder does for this specific project
- `tools: [read_file, write_file, edit_file, list_directory, search_files, grep, bash]`

Sections to include:
- **Role**: implementation-only, follows Planner Report, produces Coder Report for validator
- **Project Context**: full stack details (language version, architecture, modules, async model, HTTP, DB, error handling, object mapping, DI, logging)
- **Language Rules**: list language-version-specific constraints (e.g. JDK 1.8 — no var/records/sealed; JDK 25 — records and pattern matching allowed; Kotlin — coroutines required)
- **Per-layer implementation rules**: one section per module type, describing:
  - What annotations or patterns to use
  - What NOT to do (e.g. no inline mapping, no Log.*, no findViewById)
  - How to handle errors at this layer
  - How to use the object mapping library
- **Error handling rules**: concrete structure with example based on the project's chosen approach
- **Test rules**: when to write tests (based on collected preference), which framework to use, what patterns to follow
- **File operations**: write files directly, no confirmation, output Coder Report after all steps
- **Output Format**: Coder Report with sections — Task Summary, Completed Steps table, Deviations from Plan, Test Files Written, Object Mapper Changes, DI Changes, Known Limitations, Checklist for Validator
- **Context handoff rules**:
  - The Coder Report is the sole context passed to the validator — it must be fully self-contained
  - Completed Steps table must include the exact file path for every file created or modified
  - Checklist for Validator must restate each Acceptance Criterion from the Planner Report verbatim — do not rephrase
  - Any deviation from the Planner Report must be explained with the reason; the validator must be able to understand what changed and why without reading the Planner Report
- **Constraints**: no out-of-scope changes, no refactoring unless specified, enforce all library/logging/mapping rules

---

#### File 4: `.claude/agents/validator.md`

Frontmatter:
- `name: validator`
- `model: claude-sonnet-4-6` — test failure analysis and fixing requires strong reasoning
- `description:` one paragraph describing what the validator does for this specific project
- `tools:` include bash patterns for gradle directly in the tools list:
  ```yaml
  tools:
    - read_file
    - write_file
    - edit_file
    - list_directory
    - search_files
    - grep
    - Bash(./gradlew:*)
    - Bash(gradle:*)
  ```

Sections to include:
- **Role**: test execution and fix specialist, produces Validator Report
- **Project Context**: module structure, test stack
- **Test Stack**: list frameworks and test file locations
- **Input**: expected Coder Report contents; instruct to request if missing
- **Validation Strategy**:
  - Step 1: Identify affected modules from Coder Report (scope to modified modules only)
  - Step 2: Run unit tests per affected module (provide correct CLI commands for the build tool)
  - Step 3: Evaluate results against acceptance criteria checklist
  - Step 4: Handle failures — identify root cause category, fix directly, re-run, max 3 attempts
  - Step 5: Retry limit — document unresolved failures after 3 attempts
- **Fix Constraints**: scope limited to Coder Report files, no new dependencies, no skipping tests, no port/interface changes to workaround failures
- **Output Format**: Validator Report with sections — Task Summary, Modules Tested table, Acceptance Criteria table, Fixes Applied table, Unresolved Failures table, Final Status (PASSED / PASSED WITH LIMITATIONS / FAILED), Notes
- **Context handoff rules**:
  - The Validator Report is the final output of the pipeline — it must be readable as a standalone summary
  - Acceptance Criteria table must show every criterion from the Coder Report checklist and its result
  - Fixes Applied table must include the exact file path, the root cause category, and a one-line description of the fix for every change made
  - Unresolved Failures must include the full test name, the final error message, and the number of attempts made
- **Constraints**: module-scoped testing only, no confirmation before fixes, report all attempts

---

#### File 5: `.claude/commands/explore.md`

Content:
- Command name: `explore`
- Usage: `/explore <task description>`
- Description: invokes explorer agent for pre-task codebase analysis
- Instructions: invoke explorer agent with $ARGUMENTS; list the key exploration responsibilities specific to this project's architecture; end by asking user whether to proceed to planning

---

#### File 6: `.claude/commands/planning.md`

Content:
- Command name: `planning`
- Usage: `/planning <task description>`
- Instructions: invoke planner agent with $ARGUMENTS; if Explorer Report missing, invoke explorer first; list planning responsibilities specific to this project; display Planner Report and ask user whether to proceed to coding

---

#### File 7: `.claude/commands/code.md`

Content:
- Command name: `code`
- Usage: `/code <task description>`
- Instructions: invoke coder agent with $ARGUMENTS; if Planner Report missing, invoke planner first (which invokes explorer if needed); list key implementation rules specific to this project's stack; end by asking user whether to proceed to validation

---

#### File 8: `.claude/commands/validate.md`

Content:
- Command name: `validate`
- Usage: `/validate <task description>`
- Instructions: invoke validator agent with $ARGUMENTS; if Coder Report missing, invoke coder first; list module-scoped test execution steps; summarize final status after completion

---

#### File 9: `.claude/commands/solve-task.md`

Content:
- Command name: `solve-task`
- Usage: `/solve-task <task description>`
- Description: full pipeline orchestrator — explorer → planner → coder → validator

Pipeline stages:
- **Stage 1 (Explore)**: invoke explorer, proceed automatically to Stage 2
- **Stage 2 (Plan)**: invoke planner; if `planner_pause` is Yes — display Planner Report and wait for user confirmation; if No — proceed automatically
- **Stage 3 (Code)**: invoke coder with Planner Report, proceed automatically to Stage 4
- **Stage 4 (Validate)**: invoke validator with Coder Report, display final status

Pipeline completion summary block:
```
## Pipeline Summary
### Task / ### Stage Results table / ### Files Changed / ### Unresolved Issues
```

Pipeline pause conditions (apply regardless of planner_pause setting):
- Explorer finds no relevant files
- Planner marks any step as "NEEDS CLARIFICATION"
- Coder encounters an ambiguous step
- Validator has unresolved failures after 3 retries
- Any agent produces an incomplete report

When pausing: state which stage, what the blocker is, what is needed to continue.

---

### Phase 3: Completion Summary

After all 9 files are written, output the following:

```
## Setup Complete

### Project
(project name or one-line description)

### Files Created
.claude/agents/explorer.md
.claude/agents/planner.md
.claude/agents/coder.md
.claude/agents/validator.md
.claude/commands/explore.md
.claude/commands/planning.md
.claude/commands/code.md
.claude/commands/validate.md
.claude/commands/solve-task.md

### Key Decisions
| Item | Value |
|------|-------|
| Language | ... |
| Architecture | ... |
| Error Handling | ... |
| Object Mapping | ... |
| Test Framework | ... |
| Planner Pause | Yes / No |

### Usage
Run `/solve-task <task>` to execute the full pipeline.
Run `/explore`, `/planning`, `/code`, `/validate` to invoke individual agents.
```
