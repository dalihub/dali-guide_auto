---
title: 상태 이벤트
sidebar_label: 상태 이벤트
category: input-interaction
---

# 상태 이벤트 {#state-event}

`Dali::Ui::StateEvent`는 하나의 `Dali::Ui::View` 상태 전환을 설명하며, 전환이 구체적인 입력 이벤트에서 시작된 경우 애플리케이션 코드에 이전 상태, 현재 상태, 입력 원인을 제공합니다.

## 목차 {#table-of-contents}

- [뷰 상태 전환에 반응하기](#reacting-to-view-state-transitions)
- [추가, 제거 및 변경된 상태 감지하기](#detecting-added-removed-and-changed-states)
- [이전 및 현재 상태 스냅샷 읽기](#reading-the-previous-and-current-state-snapshots)
- [입력 이벤트 원인 사용하기](#using-the-input-event-cause)
- [상태 핸들러를 작게 유지하기](#keeping-state-handlers-small)

## 뷰 상태 전환에 반응하기 {#reacting-to-view-state-transitions}

상태 이벤트는 `Dali::Ui::View`의 상태가 변경될 때 `Dali::Ui::View::StateChangedSignal`을 통해 전달됩니다. 애플리케이션 코드에서는 이벤트를 수명이 짧은 시그널 매개변수로 처리하고, `Dali::Ui::StateEvent`에서 전환 정보를 읽습니다.

```cpp
void OnStateChanged(Dali::Ui::View view, Dali::Ui::StateEvent event)
{
  if(event.Added(Dali::Ui::ViewState::FOCUSED))
  {
    // The view gained focus in this transition.
  }

  if(event.Removed(Dali::Ui::ViewState::PRESSED))
  {
    // The view is no longer pressed.
  }
}
```

`Dali::Ui::StateEvent`는 핸들 타입입니다. 콜백에서 이벤트를 다른 로컬 헬퍼로 전달해야 하는 경우 복사하거나 대입할 수 있습니다.

```cpp
void ForwardStateEvent(Dali::Ui::StateEvent event)
{
  Dali::Ui::StateEvent copiedEvent;
  copiedEvent = event;

  if(copiedEvent.Changed(Dali::Ui::ViewState::SELECTED))
  {
    // Selection state toggled.
  }
}
```

## 추가, 제거 및 변경된 상태 감지하기 {#detecting-added-removed-and-changed-states}

특정 `Dali::Ui::ViewState`에 관심이 있을 때는 `Dali::Ui::StateEvent::Added`, `Dali::Ui::StateEvent::Removed`, `Dali::Ui::StateEvent::Changed`를 사용합니다.

```cpp
void UpdateFocusDecoration(Dali::Ui::StateEvent event)
{
  if(event.Added(Dali::Ui::ViewState::FOCUSED))
  {
    ShowFocusDecoration();
  }
  else if(event.Removed(Dali::Ui::ViewState::FOCUSED))
  {
    HideFocusDecoration();
  }
}
```

`Dali::Ui::StateEvent::Changed`는 동일한 업데이트 경로에서 전환의 양방향을 모두 처리할 수 있을 때 유용합니다.

```cpp
void RefreshPressedVisual(Dali::Ui::StateEvent event)
{
  if(!event.Changed(Dali::Ui::ViewState::PRESSED))
  {
    return;
  }

  const bool pressed = event.GetCurrent().Contains(Dali::Ui::ViewState::PRESSED);
  SetPressedVisual(pressed);
}
```

여러 상태가 조합되는 경우 시각 표현이나 동작에 중요한 각 상태를 개별적으로 검사합니다.

```cpp
void RefreshSelectionAndFocus(Dali::Ui::StateEvent event)
{
  const bool selectionChanged = event.Changed(Dali::Ui::ViewState::SELECTED);
  const bool focusChanged     = event.Changed(Dali::Ui::ViewState::FOCUSED);

  if(selectionChanged || focusChanged)
  {
    const Dali::Ui::ViewState& current = event.GetCurrent();

    SetSelectedVisual(current.Contains(Dali::Ui::ViewState::SELECTED));
    SetFocusedVisual(current.Contains(Dali::Ui::ViewState::FOCUSED));
  }
}
```

## 이전 및 현재 상태 스냅샷 읽기 {#reading-the-previous-and-current-state-snapshots}

`Dali::Ui::StateEvent::GetPrev`는 전환 전 상태를 반환합니다. `Dali::Ui::StateEvent::GetCurrent`는 전환 후 상태를 반환합니다. 이러한 스냅샷을 사용하면 핸들러 하나가 단일 플래그뿐 아니라 전체 상태 변경을 파악할 수 있습니다.

```cpp
void UpdateDisabledVisual(Dali::Ui::StateEvent event)
{
  const Dali::Ui::ViewState& previous = event.GetPrev();
  const Dali::Ui::ViewState& current  = event.GetCurrent();

  const bool wasDisabled = previous.Contains(Dali::Ui::ViewState::DISABLED);
  const bool isDisabled  = current.Contains(Dali::Ui::ViewState::DISABLED);

  if(wasDisabled != isDisabled)
  {
    SetDisabledVisual(isDisabled);
  }
}
```

현재 상태에는 동시에 둘 이상의 상태가 포함될 수 있습니다. 컴포넌트에서 필요한 모든 상태에 대해 `Dali::Ui::ViewState` 스냅샷을 확인합니다.

```cpp
void RefreshButtonChrome(Dali::Ui::StateEvent event)
{
  const Dali::Ui::ViewState& current = event.GetCurrent();

  const bool focused  = current.Contains(Dali::Ui::ViewState::FOCUSED);
  const bool pressed  = current.Contains(Dali::Ui::ViewState::PRESSED);
  const bool selected = current.Contains(Dali::Ui::ViewState::SELECTED);

  ApplyButtonChrome(focused, pressed, selected);
}
```

## 입력 이벤트 원인 사용하기 {#using-the-input-event-cause}

상태 전환은 입력 때문에 발생할 수도 있고, 프로그램 코드나 프레임워크의 상태 변경 때문에 발생할 수도 있습니다. `Dali::Ui::StateEvent::GetInputEventType`은 타입별 접근자를 통해 어떤 종류의 입력을 사용할 수 있는지 알려 주며, 구체적인 입력 이벤트에서 시작되지 않은 변경인 경우 `Dali::Ui::InputEventType::NONE`을 보고합니다.

```cpp
void DispatchStateCause(Dali::Ui::StateEvent event)
{
  switch(event.GetInputEventType())
  {
    case Dali::Ui::InputEventType::TOUCH_EVENT:
    {
      const Dali::TouchEvent& touchEvent = event.GetTouchEvent();
      HandleTouchStateChange(touchEvent);
      break;
    }

    case Dali::Ui::InputEventType::KEY_EVENT:
    {
      const Dali::KeyEvent& keyEvent = event.GetKeyEvent();
      HandleKeyStateChange(keyEvent);
      break;
    }

    case Dali::Ui::InputEventType::TAP_GESTURE:
    {
      const Dali::TapGesture& tapGesture = event.GetTapGesture();
      HandleTapStateChange(tapGesture);
      break;
    }

    case Dali::Ui::InputEventType::LONG_PRESS_GESTURE:
    {
      const Dali::LongPressGesture& longPressGesture = event.GetLongPressGesture();
      HandleLongPressStateChange(longPressGesture);
      break;
    }

    case Dali::Ui::InputEventType::WHEEL_EVENT:
    {
      const Dali::WheelEvent& wheelEvent = event.GetWheelEvent();
      HandleWheelStateChange(wheelEvent);
      break;
    }

    case Dali::Ui::InputEventType::NONE:
    case Dali::Ui::InputEventType::RESERVED:
    {
      HandleNonInputStateChange();
      break;
    }
  }
}
```

즉시 분기하지 않고 일반 입력 래퍼를 전달하려면 `Dali::Ui::StateEvent::GetCause`를 사용합니다.

```cpp
void QueueStateTransition(Dali::Ui::StateEvent event)
{
  const Dali::Ui::InputEvent& cause = event.GetCause();

  QueueStateRecord(event.GetPrev(), event.GetCurrent(), cause);
}
```

`Dali::Ui::StateEvent::GetKeyEvent` 같은 타입별 getter는 `Dali::Ui::StateEvent::GetInputEventType`이 해당 `Dali::Ui::InputEventType`과 일치하는지 확인한 뒤에만 호출합니다.

## 상태 핸들러를 작게 유지하기 {#keeping-state-handlers-small}

상태 콜백은 시각 표현을 업데이트하거나, 애플리케이션 동작을 트리거하거나, 간결한 전환 데이터를 전달하는 데 사용할 수 있습니다. 콜백은 전환 처리에 집중하도록 유지하고, 더 큰 작업은 헬퍼 함수로 옮깁니다.

```cpp
void OnComponentStateChanged(Dali::Ui::View view, Dali::Ui::StateEvent event)
{
  if(event.Changed(Dali::Ui::ViewState::FOCUSED))
  {
    UpdateFocusDecoration(view, event.GetCurrent());
  }

  if(event.Changed(Dali::Ui::ViewState::PRESSED))
  {
    UpdatePressedFeedback(view, event.GetCurrent());
  }

  if(event.Changed(Dali::Ui::ViewState::DISABLED))
  {
    UpdateInputAvailability(view, event.GetCurrent());
  }
}
```

한 상태 핸들러가 다른 상태 업데이트를 유발하면 DALi는 다음 전환을 전달하기 전에 현재 상태 변경 배치에 대한 알림을 완료합니다. 각 호출이 자신이 받은 `Dali::Ui::StateEvent`에만 반응하도록 핸들러를 작성합니다.

```cpp
void OnFocusableButtonStateChanged(Dali::Ui::View view, Dali::Ui::StateEvent event)
{
  if(event.Added(Dali::Ui::ViewState::FOCUSED))
  {
    ShowFocusDecoration(view);
  }

  if(event.Added(Dali::Ui::ViewState::PRESSED))
  {
    StartPressedAnimation(view);
  }

  if(event.Removed(Dali::Ui::ViewState::PRESSED))
  {
    StopPressedAnimation(view);
  }
}
```
