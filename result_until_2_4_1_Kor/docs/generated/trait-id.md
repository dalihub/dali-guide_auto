---
title: 트레이트 ID
sidebar_label: 트레이트 ID
category: utilities
---

# 트레이트 ID {#trait-id}

`Dali::Ui::TraitId`는 `Dali::Ui::View`와 함께 사용되는 논리적 트레이트 타입을 식별하는 경량 핸들입니다.

## 목차 {#table-of-contents}

- [안정적인 트레이트 식별자 할당](#allocate-a-stable-trait-identifier)
- [트레이트 식별자 비교](#compare-trait-identifiers)
- [진단을 위한 원시 값 사용](#use-the-raw-value-for-diagnostics)

## 안정적인 트레이트 식별자 할당 {#allocate-a-stable-trait-identifier}

각 논리적 트레이트 타입마다 `Dali::Ui::TraitId::Alloc()`을 한 번 사용한 다음, dali-ui 애플리케이션이나 확장 코드에서 해당 트레이트를 인식해야 하는 모든 위치에서 그 `Dali::Ui::TraitId`를 재사용합니다. 결과를 정적 저장소에 보관하여 동일한 트레이트 역할이 프로세스 수명 동안 하나의 식별자를 유지하도록 합니다.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kSelectionStateTraitId = Dali::Ui::TraitId::Alloc();
const Dali::Ui::TraitId kValidationStateTraitId = Dali::Ui::TraitId::Alloc();
}

Dali::Ui::TraitId GetSelectionStateTraitId()
{
  return kSelectionStateTraitId;
}

Dali::Ui::TraitId GetValidationStateTraitId()
{
  return kValidationStateTraitId;
}
```

`Dali::Ui::View`에 대한 동작을 검사하거나 연결할 때마다 새 `Dali::Ui::TraitId`를 할당하지 마십시오. `Dali::Ui::TraitId::Alloc()`을 호출할 때마다 새 식별자가 반환됩니다.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kKeyboardFocusTraitId = Dali::Ui::TraitId::Alloc();
}

Dali::Ui::TraitId KeyboardFocusTraitId()
{
  return kKeyboardFocusTraitId;
}
```

## 트레이트 식별자 비교 {#compare-trait-identifiers}

`Dali::Ui::TraitId`는 `Dali::Ui::TraitId::operator==`를 통한 직접 동등성 검사와 `Dali::Ui::TraitId::operator!=`를 통한 비동등성 검사를 지원합니다. 이는 View 중심 코드 경로에서 트레이트별 로직을 라우팅하는 일반적인 방법입니다.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kSelectionTraitId = Dali::Ui::TraitId::Alloc();
const Dali::Ui::TraitId kActivationTraitId = Dali::Ui::TraitId::Alloc();
}

bool IsSelectionTrait(Dali::Ui::TraitId traitId)
{
  return traitId == kSelectionTraitId;
}

bool IsDifferentTrait(Dali::Ui::TraitId lhs, Dali::Ui::TraitId rhs)
{
  return lhs != rhs;
}

const char* GetTraitName(Dali::Ui::TraitId traitId)
{
  if(traitId == kSelectionTraitId)
  {
    return "selection";
  }

  if(traitId == kActivationTraitId)
  {
    return "activation";
  }

  return "unknown";
}
```

콜백이나 헬퍼가 `Dali::Ui::TraitId`를 받으면 새로 할당된 값과 비교하지 말고 해당 기능이 소유한 식별자와 비교합니다.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kEnabledStateTraitId = Dali::Ui::TraitId::Alloc();
}

bool ShouldUpdateEnabledState(Dali::Ui::TraitId changedTraitId)
{
  return changedTraitId == kEnabledStateTraitId;
}
```

## 진단을 위한 원시 값 사용 {#use-the-raw-value-for-diagnostics}

`Dali::Ui::TraitId::value`는 숫자 식별자를 노출합니다. 일반 정수 표현이 필요할 때 진단, 로깅 또는 메모리 내 조회 키에 사용합니다. 일반 애플리케이션 로직의 식별성 검사는 `Dali::Ui::TraitId::operator==` 또는 `Dali::Ui::TraitId::operator!=`를 기반으로 유지하고, 숫자 값을 영구 파일 또는 구성 ID로 취급하지 마십시오.

```cpp
#include <cstdint>
#include <dali-ui-foundation/public-api/trait-id.h>

uint32_t GetTraitDebugValue(Dali::Ui::TraitId traitId)
{
  return traitId.value;
}
```

간결한 진단 레코드는 별도의 애플리케이션 수준 식별자를 도입하지 않고도 `Dali::Ui::TraitId`와 해당 `Dali::Ui::TraitId::value`를 모두 저장할 수 있습니다.

```cpp
#include <cstdint>
#include <dali-ui-foundation/public-api/trait-id.h>

struct TraitDebugRecord
{
  Dali::Ui::TraitId traitId;
  uint32_t numericValue;
};

TraitDebugRecord MakeTraitDebugRecord(Dali::Ui::TraitId traitId)
{
  return TraitDebugRecord{
    traitId,
    traitId.value
  };
}
```
