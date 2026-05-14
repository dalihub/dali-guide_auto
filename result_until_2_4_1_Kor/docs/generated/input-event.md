---
title: 입력 이벤트
sidebar_label: 입력 이벤트
category: input-interaction
---

# 입력 이벤트 {#input-event}

`Dali::Ui::InputEvent`는 dali-ui 상호작용과 상태 변경이 어떤 입력 또는 비입력 원인에서 비롯되었는지 식별합니다.

## 목차 {#table-of-contents}

- [`InputEvent`가 나타내는 것](#what-inputevent-represents)
- [입력 원인 생성](#creating-an-input-cause)
- [형식이 지정된 이벤트 페이로드 읽기](#reading-the-typed-event-payload)
- [비입력 원인 사용](#using-a-non-input-cause)
- [View 로직을 통해 입력 이벤트 전달](#passing-input-events-through-view-logic)

## `InputEvent`가 나타내는 것 {#what-inputevent-represents}

dali-ui 애플리케이션의 View 상호작용 로직은 상태가 왜 변경되었는지 알아야 하는 경우가 많습니다. `Dali::Ui::InputEvent`는 애플리케이션에서 이 원인을 다루기 위한 핸들입니다. 터치 이벤트, 키 이벤트, 탭 제스처, 롱 프레스 제스처, 휠 이벤트 같은 구체적인 입력 페이로드를 나타낼 수도 있고, 비입력 변경을 나타낼 수도 있습니다.

특정 `Dali::Ui::InputEvent`에 어떤 형식별 getter를 사용할 수 있는지 결정하기 전에 `GetInputEventType()`을 사용하세요.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

void StoreLastInputCause(const Dali::Ui::InputEvent& event)
{
  Dali::Ui::InputEvent lastEvent;
  lastEvent = event;

  auto eventType = lastEvent.GetInputEventType();
  (void)eventType;
}
```

`Dali::Ui::InputEvent`는 핸들 타입입니다. `operator=`로 복사하거나 대입하면 각 페이로드 필드를 수동으로 복사하는 대신 동일한 이벤트 핸들에 대한 참조를 유지합니다.

## 입력 원인 생성 {#creating-an-input-cause}

코드에 이미 구체적인 입력 객체가 있다면, 해당 입력에 맞는 `InputEvent::New()` 오버로드로 `Dali::Ui::InputEvent`를 생성하세요. 휠 입력의 경우 원본 `Dali::WheelEvent`를 직접 전달합니다.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>
#include <dali/public-api/events/wheel-event.h>

Dali::Ui::InputEvent MakeWheelCause(const Dali::WheelEvent& wheelEvent)
{
  return Dali::Ui::InputEvent::New(wheelEvent);
}
```

생성된 이벤트는 하나의 원인 객체로 dali-ui View 컨트롤러나 상태 로직에 전달할 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>
#include <dali/public-api/events/wheel-event.h>

class ViewInteractionController
{
public:
  void OnWheel(const Dali::WheelEvent& wheelEvent)
  {
    Dali::Ui::InputEvent cause = Dali::Ui::InputEvent::New(wheelEvent);
    RememberCause(cause);
  }

private:
  void RememberCause(const Dali::Ui::InputEvent& cause)
  {
    mLastCause = cause;
  }

  Dali::Ui::InputEvent mLastCause;
};
```

## 형식이 지정된 이벤트 페이로드 읽기 {#reading-the-typed-event-payload}

`Dali::Ui::InputEvent`는 원래 이벤트 페이로드를 저장합니다. 처리 중인 이벤트 타입과 일치하는 getter를 사용하세요. 타입이 `Dali::Ui::InputEventType::WHEEL_EVENT`인 `Dali::Ui::InputEvent`에는 `GetWheelEvent()`를 사용합니다.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>
#include <dali/public-api/events/wheel-event.h>

const Dali::WheelEvent& ReadWheelEvent(const Dali::Ui::InputEvent& event)
{
  return event.GetWheelEvent();
}
```

형식별 getter는 저장된 페이로드에 대한 const 참조를 반환하며, 저장된 타입이 getter와 일치하지 않으면 assert가 발생합니다.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

void ReadKnownTypedInput(const Dali::Ui::InputEvent& event)
{
  auto inputType = event.GetInputEventType();
  (void)inputType;

  // Call the typed getter that matches the input type used by this code path.
  // Examples:
  // const auto& touchEvent = event.GetTouchEvent();
  // const auto& keyEvent = event.GetKeyEvent();
  // const auto& tapGesture = event.GetTapGesture();
  // const auto& longPressGesture = event.GetLongPressGesture();
  const auto& wheelEvent = event.GetWheelEvent();
  (void)wheelEvent;
}
```

`GetTouchEvent()`, `GetKeyEvent()`, `GetTapGesture()`, `GetLongPressGesture()`, `GetWheelEvent()`는 각각에 대응하는 원본 입력에서 생성된 이벤트에만 사용하세요.

## 비입력 원인 사용 {#using-a-non-input-cause}

모든 View 상태 변경이 직접적인 사용자 입력에서 발생하는 것은 아닙니다. 유효한 공유 `Dali::Ui::InputEvent`가 필요하지만 변경이 프로그램 코드나 프레임워크에 의해 트리거된 경우에는 `InputEvent::None()`을 사용하세요.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

const Dali::Ui::InputEvent& ProgrammaticCause()
{
  return Dali::Ui::InputEvent::None();
}
```

`InputEvent::None()`은 `Dali::Ui::InputEventType::NONE`을 가진 공유 const 참조를 반환하므로, 반복되는 비입력 경로에 적합합니다. 별도의 `NONE` 이벤트 핸들이 필요할 때만 인자 없는 `InputEvent::New()`를 사용하세요.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

class ViewStateController
{
public:
  void MarkChangedByCode()
  {
    ApplyCause(Dali::Ui::InputEvent::None());
  }

private:
  void ApplyCause(const Dali::Ui::InputEvent& cause)
  {
    mLastCause = cause;
  }

  Dali::Ui::InputEvent mLastCause;
};
```

## View 로직을 통해 입력 이벤트 전달 {#passing-input-events-through-view-logic}

입력 처리와 View 상태 로직 사이의 경계에서는 `Dali::Ui::InputEvent`를 유지하세요. dali-ui 코드는 구체적인 입력을 받은 뒤 한 번만 감싸고, `Dali::Ui::View` 기반 애플리케이션 객체에서 동작하는 헬퍼를 통해 그 원인을 전달할 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/events/wheel-event.h>

class ScrollableViewController
{
public:
  explicit ScrollableViewController(Dali::Ui::View view)
  : mView(view)
  {
  }

  void HandleWheelInput(const Dali::WheelEvent& wheelEvent)
  {
    Dali::Ui::InputEvent cause = Dali::Ui::InputEvent::New(wheelEvent);
    UpdateFromInput(cause);
  }

  void HandleProgrammaticUpdate()
  {
    UpdateFromInput(Dali::Ui::InputEvent::None());
  }

private:
  void UpdateFromInput(const Dali::Ui::InputEvent& cause)
  {
    mLastCause = cause;
    auto causeType = mLastCause.GetInputEventType();
    (void)causeType;
  }

  Dali::Ui::View mView;
  Dali::Ui::InputEvent mLastCause;
};
```

이렇게 하면 애플리케이션 모델을 `Dali::Ui::View` 중심으로 유지하면서, 상호작용별 판단에 필요한 정확한 입력 원인을 보존할 수 있습니다.
