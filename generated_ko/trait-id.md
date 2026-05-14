---
title: Trait ID
sidebar_label: Trait ID
category: views-components
---

# Trait ID

`TraitId`는 `View` 객체에 첨부된 trait의 고유 식별자를 나타내는 가벼운 핸들입니다. 각 trait 타입은 동일한 뷰의 다른 trait과 구별하기 위해 고유한 `TraitId`가 필요합니다.

## 목차

- [Trait 식별자 할당](#trait-식별자-할당)
- [TraitId 값 비교](#traitid-값-비교)
- [예약된 Trait ID](#예약된-trait-id)

## Trait 식별자 할당

`TraitId::Alloc()`을 사용하여 각 커스텀 trait 타입에 대한 고유 식별자를 할당합니다. 이 메서드를 trait 타입당 한 번 호출하고 결과를 정적 변수에 저장하여 애플리케이션 수명 동안 일관된 식별을 보장합니다.

```cpp
// 커스텀 trait에 대한 고유 ID 할당 (일반적으로 익명 네임스페이스에서)
static const TraitId kMyCustomTrait = TraitId::Alloc();
```

`TraitId::Alloc()` 메서드는 스레드 안전하고 락이 없으며 내부 원자 카운터를 사용합니다. 각 호출은 새로운 고유 `TraitId` 값을 반환합니다.

## TraitId 값 비교

`TraitId`는 식별자 값을 비교하기 위한 동등 및 부등 연산자를 제공합니다. trait 식별자로 작업할 때 trait 정체성을 확인하는 데 사용합니다.

```cpp
TraitId id1 = TraitId::Alloc();
TraitId id2 = TraitId::Alloc();
TraitId id3 = id1;

// 동등 비교
if (id1 == id3)
{
  // id1과 id3이 동일한 값을 가짐
}

// 부등 비교
if (id1 != id2)
{
  // id1과 id2가 다른 값을 가짐 (각 Alloc()은 고유 ID 반환)
}
```

로깅이나 디버깅 시 `value` 멤버를 통해 기본 숫자 값에 접근합니다:

```cpp
TraitId id = TraitId::Alloc();
uint32_t numericValue = id.value;
```

## 예약된 Trait ID

프레임워크는 내부 사용을 위해 `Dali::Ui::Integration::ReservedTraitId` 네임스페이스에 미리 정의된 `TraitId` 상수 집합을 예약합니다. 이러한 식별자는 레이아웃 매개변수 및 인터랙션 처리와 같은 내장 프레임워크 기능을 지원합니다.

프레임워크 예약 식별자:

- `Dali::Ui::Integration::ReservedTraitId::INTERACTION_TRAIT` — 인터랙션 동작 trait
- `Dali::Ui::Integration::ReservedTraitId::SELECTABLE_TRAIT` — 선택 상태 trait
- `Dali::Ui::Integration::ReservedTraitId::STATE_HANDLER_TRAIT` — 상태 처리 trait
- `Dali::Ui::Integration::ReservedTraitId::ABSOLUTE_LAYOUT_PARAMS` — 절대 레이아웃 매개변수
- `Dali::Ui::Integration::ReservedTraitId::STACK_LAYOUT_PARAMS` — 스택 레이아웃 매개변수
- `Dali::Ui::Integration::ReservedTraitId::GRID_LAYOUT_PARAMS` — 그리드 레이아웃 매개변수
- `Dali::Ui::Integration::ReservedTraitId::FLEX_LAYOUT_PARAMS` — 플렉스 레이아웃 매개변수

애플리케이션 코드는 예약된 식별자를 사용하는 대신 `TraitId::Alloc()`을 통해 커스텀 `TraitId` 값을 할당해야 합니다.
