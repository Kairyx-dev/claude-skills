# Use Tidy-First Interceptor as Rigid Process Skill

## Status

accepted

## Context and Problem Statement

Claude Code 스킬 플러그인에 Kent Beck의 "Tidy First?" 방법론을 구현하려 한다.
코드 수정 작업 시 structural change(구조 개선)와 behavioral change(동작 변경)를
항상 분리하도록 Claude를 안내하는 스킬이 필요하다.
명시적 호출(/tidy-first)과 코드 수정 시 자동 발동 둘 다 지원해야 한다.

## Decision Drivers

* Kent Beck의 핵심 원칙: structural과 behavioral change는 절대 동시에 수행하지 않는다
* Superpowers 에코시스템의 rigid process skill 패턴 (TDD 스킬과 동일한 방식)
* 모든 코드 수정에 자동 적용 — 작은 수정도 포함
* Brainstorming → Writing-plans → Implementation 파이프라인과 자연스럽게 연동
* 언어 무관, 구체적 예시 포함

## Considered Options

* Option A — Interceptor: 모든 코드 수정 전 Tidy Gate를 통과하는 rigid 4단계 프로세스
* Option B — Advisor: 명시적 호출 시만 structured process, 자동 발동은 힌트 수준
* Option C — Pre-commit: 코드 작성 후 커밋 직전에 체크리스트 실행

## Decision Outcome

Chosen option: "Option A — Interceptor", because Kent Beck의 원칙("tidy first, then change behavior")을 정확히 구현하며, "모든 코드 수정에 자동 발동"이라는 요구사항과 완벽히 일치한다. Superpowers의 TDD 스킬이 "테스트 먼저"를 강제하듯, 이 스킬은 "tidying 먼저"를 강제한다.

### Consequences

* Good: structural/behavioral 분리가 항상 보장됨
* Good: Never/Later/After/First 프레임으로 의식적 결정 강제
* Good: Superpowers rigid skill 패턴과 일관성 유지
* Bad: 모든 수정마다 Gate를 통과하므로 작은 수정에 오버헤드처럼 느껴질 수 있음

## Pros and Cons of the Options

### Option A — Interceptor

* Good: Kent Beck 원칙 정확히 구현
* Good: rigid → 규율이 명확, 무시 불가
* Good: 자동 발동 요구사항과 일치
* Bad: 사소한 수정에도 Gate 통과 필요

### Option B — Advisor

* Good: 덜 강제적, 빠른 수정 방해 없음
* Bad: "모든 수정 자동 감지" 요구사항과 충돌
* Bad: 규율이 흐려져 실질적 효과 감소

### Option C — Pre-commit

* Good: 기존 워크플로우 방해 없음
* Bad: Kent Beck 의도와 반대 — "먼저 tidy"가 아니라 "나중에 tidy"
* Bad: 방법론의 핵심 원칙을 놓침
