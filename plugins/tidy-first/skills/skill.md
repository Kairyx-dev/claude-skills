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

---

## Tidying 체크리스트

모든 tidying은 순수 구조 개선 — 관찰 가능한 동작을 바꾸지 않는다.

### 가독성

| Tidying | 설명 |
|---------|------|
| **Guard Clauses** | 중첩 조건문을 조기 반환으로 평탄화 |
| **Explaining Variable** | 복잡한 표현식을 이름 있는 변수로 추출 |
| **Explaining Constant** | 매직 넘버/문자열을 명명된 상수로 교체 |
| **Chunk Statements** | 관련 코드를 빈 줄로 그룹핑 |
| **Order by Reading Order** | 코드를 읽히는 순서대로 재배치 |

### 구조

| Tidying | 설명 |
|---------|------|
| **Extract Helper** | 재사용 가능한 로직을 별도 함수로 분리 |
| **Inline Helper** | 한 번만 쓰이는 불필요한 추상화 제거 |
| **Dead Code Removal** | 도달 불가능하거나 미사용 코드 삭제 |
| **Move Declaration and Initialization Together** | 변수 선언을 첫 사용 위치 바로 앞으로 이동 |

### 응집도 & 결합도

| Tidying | 설명 |
|---------|------|
| **Cohesion Ordering** | 함께 변하는 코드를 물리적으로 인접하게 이동 |
| **Normalize Symmetries** | 같은 의도의 코드를 동일한 패턴으로 통일 |
| **Explicit Parameters** | 암묵적 의존성을 명시적 파라미터로 교체 |

---

## 언어별 예시

언어 무관 스킬. 아래 예시는 Kotlin과 TypeScript 기준.

### Guard Clauses

```kotlin
// Before (Kotlin)
fun process(user: User?) {
    if (user != null) {
        if (user.isActive) {
            doWork(user)
        }
    }
}

// After
fun process(user: User?) {
    if (user == null) return
    if (!user.isActive) return
    doWork(user)
}
```

```typescript
// Before (TypeScript)
function process(user: User | null) {
    if (user !== null) {
        if (user.isActive) {
            doWork(user);
        }
    }
}

// After
function process(user: User | null) {
    if (user === null) return;
    if (!user.isActive) return;
    doWork(user);
}
```

### Explaining Variable

```kotlin
// Before
if (order.items.sumOf { it.price } > 100_000 && order.customer.tier == Tier.PREMIUM) {
    applyDiscount(order)
}

// After
val totalPrice = order.items.sumOf { it.price }
val isPremiumCustomer = order.customer.tier == Tier.PREMIUM
if (totalPrice > 100_000 && isPremiumCustomer) {
    applyDiscount(order)
}
```

### Cohesion Ordering

```typescript
// Before — 관련 함수가 흩어져 있음
function calculateTotal() { ... }
function formatDate() { ... }
function applyDiscount() { ... }
function parseDate() { ... }

// After — 날짜 함수끼리, 가격 함수끼리
function calculateTotal() { ... }
function applyDiscount() { ... }
function formatDate() { ... }
function parseDate() { ... }
```

---

## 제약 사항

- **Structural change와 behavioral change를 한 단계에서 혼합하지 않는다**
- **한 번에 하나의 tidying** — 별도 커밋 없이 여러 tidying을 묶지 않는다
- **Tidy gate는 모든 코드 수정에 적용** — 작은 수정도 포함
- **Structural 요청은 Gate 건너뜀** — 이미 tidying 중이다
- **After/Never/Later는 유효한 선택** — 목표는 의식적 결정이지 항상 tidying이 아니다

## 범위 외

- 성능 최적화 (부하 조건에서 관찰 가능한 동작이 바뀔 수 있음)
- 테스트 리팩토링 (별도 관심사)
- 의존성/아키텍처 재구조화 (단일 tidying에 비해 너무 큰 범위)
