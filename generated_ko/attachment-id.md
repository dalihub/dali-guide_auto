---
title: 첨부 ID
sidebar_label: 첨부 ID
category: views-components
---

# 첨부 ID

`Dali::Ui::AttachmentId`는 `View` 인스턴스에 커스텀 데이터를 첨부하기 위한 가벼운 식별자입니다. 첨부를 사용하여 서브클래싱하거나 핸들 타입에 필드를 추가하지 않고도 객체별 상태를 저장합니다.

## 목차

- [첨부 ID 할당](#첨부-id-할당)
- [뷰에 데이터 첨부](#뷰에-데이터-첨부)
- [첨부 데이터 조회](#첨부-데이터-조회)
- [첨부 해제 및 재첨부](#첨부-해제-및-재첨부)
- [비교 연산](#비교-연산)

## 첨부 ID 할당

논리적 첨부 타입당 하나의 `AttachmentId`를 `AttachmentId::Alloc()`으로 할당합니다. 반환된 ID를 정적 저장소에 저장하여 `SetAttachment()`, `GetAttachment()`, `DetachAttachment()` 호출에서 재사용할 수 있도록 합니다.

```cpp
#include <dali-ui-foundation/public-api/attachment-id.h>

using namespace Dali::Ui;

// 익명 네임스페이스 또는 정적 멤버로
namespace
{
const AttachmentId COUNTER_DATA_ID = AttachmentId::Alloc();
}
```

객체 인스턴스별 또는 API 호출별로 새 ID를 할당하지 마세요. 반복적인 일회성 할당은 프로세스 전체 ID 공간을 낭비하고 이전에 저장된 첨부를 조회할 수 없게 만듭니다.

`AttachmentId::Alloc()`은 스레드 안전하고 락 프리입니다. 내부 원자 카운터를 사용합니다. `uint32_t` ID 공간이 소진되면 래핑 대신 어설션이 발생합니다.

## 뷰에 데이터 첨부

`View::SetAttachment()`를 사용하여 `View`에 커스텀 데이터를 첨부합니다. 데이터 소유권은 `Dali::UniquePtr<T>`을 통해 View로 이전됩니다.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/object/unique-ptr.h>

using namespace Dali;
using namespace Dali::Ui;

struct CounterData
{
  explicit CounterData(Label label)
  : label(label)
  {
  }

  Label    label;
  uint32_t count{0u};
};

// View에 데이터 첨부
InteractiveView counter = InteractiveView::New()
  .SetAttachment(COUNTER_DATA_ID, MakeUnique<CounterData>(label));
```

동일한 ID에 다른 첨부가 이미 존재하면 파괴되고 교체됩니다. 저장된 첨부는 제거, 교체 또는 View 구현이 파괴될 때 파괴됩니다.

## 첨부 데이터 조회

`View::GetAttachment()`를 사용하여 저장된 데이터에 대한 포인터를 조회합니다. 반환된 포인터는 View가 소유합니다.

```cpp
void UpdateLabel(View view)
{
  CounterData* data = view.GetAttachment<CounterData>(COUNTER_DATA_ID);
  if(data)
  {
    char text[64];
    std::snprintf(text, sizeof(text), "Count: %u", data->count);
    data->label.SetText(text);
  }
}
```

포인터는 첨부가 제거, 교체되거나 View가 파괴된 후 유효하지 않게 됩니다. 반환된 포인터를 사용하기 전에 항상 `nullptr`를 확인하세요.

## 첨부 해제 및 재첨부

`View::DetachAttachment()`를 사용하여 첨부를 제거하고 데이터의 소유권을 가져옵니다. 이는 데이터를 수정하고 재첨부할 때 유용합니다.

```cpp
void OnResetClicked(View view, AttachmentId id)
{
  UniquePtr<CounterData> data = view.DetachAttachment<CounterData>(id);
  if(data)
  {
    data->count = 0u;
    data->label.SetText("Count: 0");
    view.SetAttachment(id, Move(data));
  }
}
```

`View::RemoveAttachment()`를 사용하여 첨부를 조회하지 않고 파괴합니다. 첨부가 제거되면 `true`를, 해당 ID의 첨부가 없으면 `false`를 반환합니다:

```cpp
bool removed = view.RemoveAttachment(COUNTER_DATA_ID);
```

## 비교 연산

`AttachmentId`는 `operator==`과 `operator!=`를 통한 동등 및 부등 비교를 지원합니다.

```cpp
AttachmentId id1 = AttachmentId::Alloc();
AttachmentId id2 = AttachmentId::Alloc();

if(id1 != id2)
{
  // 다른 첨부 슬롯
}

if(id1 == id1)
{
  // 동일한 첨부 슬롯
}
```

`value` 멤버는 디버깅이나 로깅 목적으로 기본 `uint32_t` 식별자에 대한 접근을 제공합니다.
