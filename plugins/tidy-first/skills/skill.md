---
name: tidy-first
description: Use when making any code change — automatically intercepts before behavioral changes to assess whether structural improvements (tidyings) should come first. Also invoked explicitly via /tidy-first.
user-invocable: true
---

# Tidy First

Kent Beck의 "Tidy First?" 방법론을 구현한 rigid process 스킬.

**핵심 원칙:** Structural change와 behavioral change는 절대 동시에 수행하지 않는다.

**스킬 타입:** Rigid — TDD처럼 절대 건너뛰지 않는다.

## 발동 조건

**자동 인터셉트:** 코드를 수정하거나 작성하려는 순간, behavioral change를 수행하기 전에 항상 Tidy Gate를 통과한다.

**명시적 호출:**
```
/tidy-first                    → 현재 작업 중인 코드에 적용
/tidy-first <파일명>           → 특정 파일 대상
/tidy-first <설명>             → 설명 기반으로 적용
```

**예외 (Gate 건너뜀):** 요청 자체가 이미 structural change인 경우 (순수 리팩토링 요청) — 이미 tidying 중이므로 Gate 불필요.

## Superpowers 파이프라인 내 위치

```
Brainstorming → Writing-plans → [TIDY-FIRST GATE] → Implementation → Code Review
```

Process skill로서 HOW를 결정한다 — 코드를 실제로 쓰기 직전 마지막 게이트.
