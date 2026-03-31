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

## Tidy Gate — 4단계 프로세스

### Step 1: Analyze

요청된 변경의 성격을 파악한다.

- **Behavioral change** (새 기능, 버그픽스, 동작 변경) → Step 2로 진행
- **Structural change** (리팩토링 요청 자체) → Gate 없이 바로 수행

### Step 2: Assess

아래 3가지 질문에 답해 tidying 필요성을 판단한다.

1. 이 코드를 변경하려면 먼저 이해를 돕는 정리가 필요한가?
2. Tidying하면 behavioral change가 더 쉽거나 안전해지는가?
3. Tidying 비용이 미래 이득보다 작은가?

**결정:**
- 하나라도 Yes → **Tidy First** (Step 3으로)
- 모두 No → 바로 behavioral change 수행

### Step 3: Tidy

체크리스트에서 적용할 tidying을 선택해 수행한다.

- 한 번에 하나의 tidying만 적용
- 이 단계에서 동작 변경 없음 — 오직 구조만
- Tidying을 behavioral change와 **별도 커밋**으로 분리 권장

### Step 4: Behavioral Change

Tidying 완료 후 원래 요청을 수행한다.

---

## 타이밍 결정 프레임워크 (Never / Later / After / First)

| 결정 | 의미 | 사용 시점 |
|------|------|----------|
| **Never** | 이 코드는 다시 건드리지 않을 것 | Tidying 완전 생략 |
| **Later** | Tidying이 가치 있지만 지금은 아님 | 메모만 남기고 behavioral change 진행 |
| **After** | 변경 후 tidy | 실험적 변경, 스파이크, 방향이 불확실할 때 |
| **First** | 먼저 tidy 후 변경 (기본값) | 대부분의 피처/버그픽스 작업 |

목표는 "항상 tidy"가 아니라 **의식적인 결정**이다. Never/Later/After도 유효한 선택이다.
