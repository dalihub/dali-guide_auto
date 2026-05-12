---
title: Input Event
sidebar_label: Input Event
category: input-interaction
---

# Input Event

`Dali::Ui::InputEvent` carries the input cause for dali-ui view callbacks and state changes.

## Table of Contents

- [Using `InputEvent` in View Callbacks](#using-inputevent-in-view-callbacks)
- [Branching by Input Event Type](#branching-by-input-event-type)
- [Accessing the Original Input Data](#accessing-the-original-input-data)
- [Representing Non-Input Changes](#representing-non-input-changes)
- [Creating an Input Event from a Wheel Event](#creating-an-input-event-from-a-wheel-event)

## Using `InputEvent` in View Callbacks

In a dali-ui application, treat `Dali::Ui::View` as the app-facing object that receives interaction context. Callback code commonly receives a `Dali::Ui::View` together with a `Dali::Ui::InputEvent`; the view identifies the UI object, and the input event identifies what caused the callback.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

class Controller : public ConnectionTracker
{
public:
  bool OnClicked(View view, InputEvent event)
  {
    const InputEventType type = event.GetInputEventType();

    if(type == InputEventType::TAP_GESTURE)
    {
      const auto& tap = event.GetTapGesture();
      HandleTap(view, tap);
      return true;
    }

    if(type == InputEventType::KEY_EVENT)
    {
      const auto& key = event.GetKeyEvent();
      HandleKey(view, key);
      return true;
    }

    return false;
  }

private:
  void HandleTap(View view, const TapGesture& tap);
  void HandleKey(View view, const KeyEvent& key);
};
```

`Dali::Ui::InputEvent` is a handle type. It can be copied or assigned when an application needs to keep the same input cause while dispatching work inside its own code.

```cpp
class InputRouter
{
public:
  void StoreLastInput(InputEvent event)
  {
    mLastInput = event;
  }

  InputEvent GetLastInput() const
  {
    return mLastInput;
  }

private:
  InputEvent mLastInput;
};
```

## Branching by Input Event Type

Call `Dali::Ui::InputEvent::GetInputEventType()` before reading typed event data. The returned `Dali::Ui::InputEventType` tells you which typed getter is valid for the current input.

```cpp
void DispatchInput(View view, InputEvent event)
{
  switch(event.GetInputEventType())
  {
    case InputEventType::TOUCH_EVENT:
    {
      const auto& touch = event.GetTouchEvent();
      HandleTouch(view, touch);
      break;
    }

    case InputEventType::KEY_EVENT:
    {
      const auto& key = event.GetKeyEvent();
      HandleKey(view, key);
      break;
    }

    case InputEventType::TAP_GESTURE:
    {
      const auto& tap = event.GetTapGesture();
      HandleTap(view, tap);
      break;
    }

    case InputEventType::LONG_PRESS_GESTURE:
    {
      const auto& longPress = event.GetLongPressGesture();
      HandleLongPress(view, longPress);
      break;
    }

    case InputEventType::WHEEL_EVENT:
    {
      const auto& wheel = event.GetWheelEvent();
      HandleWheel(view, wheel);
      break;
    }

    case InputEventType::NONE:
    default:
    {
      HandleProgrammaticChange(view);
      break;
    }
  }
}
```

## Accessing the Original Input Data

`Dali::Ui::InputEvent` stores one original input object. Use the getter that matches `GetInputEventType()`:

- `Dali::Ui::InputEvent::GetTouchEvent()` for `Dali::Ui::InputEventType::TOUCH_EVENT`
- `Dali::Ui::InputEvent::GetKeyEvent()` for `Dali::Ui::InputEventType::KEY_EVENT`
- `Dali::Ui::InputEvent::GetTapGesture()` for `Dali::Ui::InputEventType::TAP_GESTURE`
- `Dali::Ui::InputEvent::GetLongPressGesture()` for `Dali::Ui::InputEventType::LONG_PRESS_GESTURE`
- `Dali::Ui::InputEvent::GetWheelEvent()` for `Dali::Ui::InputEventType::WHEEL_EVENT`

```cpp
void UpdatePressedVisual(View view, InputEvent event)
{
  if(event.GetInputEventType() == InputEventType::TOUCH_EVENT)
  {
    const auto& touch = event.GetTouchEvent();
    ApplyTouchPressedStyle(view, touch);
    return;
  }

  if(event.GetInputEventType() == InputEventType::TAP_GESTURE)
  {
    const auto& tap = event.GetTapGesture();
    ApplyTapPressedStyle(view, tap);
    return;
  }

  ApplyDefaultPressedStyle(view);
}
```

Keep typed access local to the branch that checked the type. This keeps callback code clear and avoids asking an `InputEvent` for a stored event kind it does not contain.

```cpp
bool OnLongPressed(View view, InputEvent event)
{
  if(event.GetInputEventType() != InputEventType::LONG_PRESS_GESTURE)
  {
    return false;
  }

  const auto& longPress = event.GetLongPressGesture();
  OpenContextMenu(view, longPress);
  return true;
}
```

## Representing Non-Input Changes

Some view state changes are not caused by a concrete touch, key, tap, long press, or wheel input. Use `Dali::Ui::InputEvent::None()` when your code needs a valid `Dali::Ui::InputEvent` value for that case.

```cpp
void NotifySelectionChanged(View view, bool selected)
{
  const InputEvent& cause = InputEvent::None();

  if(cause.GetInputEventType() == InputEventType::NONE)
  {
    EmitSelectionChanged(view, selected, cause);
  }
}
```

Use `Dali::Ui::InputEvent::New()` when you need a separate `Dali::Ui::InputEvent` handle whose type is also `Dali::Ui::InputEventType::NONE`.

```cpp
InputEvent MakeProgrammaticCause()
{
  InputEvent event = InputEvent::New();

  if(event.GetInputEventType() == InputEventType::NONE)
  {
    return event;
  }

  return InputEvent::None();
}
```

## Creating an Input Event from a Wheel Event

When application or integration code already has a wheel event and needs to pass it through dali-ui input-cause handling, create a `Dali::Ui::InputEvent` with `Dali::Ui::InputEvent::New(const WheelEvent&)`.

```cpp
InputEvent WrapWheelInput(const WheelEvent& wheelEvent)
{
  InputEvent event = InputEvent::New(wheelEvent);

  if(event.GetInputEventType() == InputEventType::WHEEL_EVENT)
  {
    const auto& originalWheel = event.GetWheelEvent();
    StoreWheelForLater(originalWheel);
  }

  return event;
}
```

The resulting `Dali::Ui::InputEvent` can then be routed through the same `Dali::Ui::View`-oriented callback helpers used for other input causes.

```cpp
void RouteWheel(View view, const WheelEvent& wheelEvent)
{
  InputEvent event = InputEvent::New(wheelEvent);
  DispatchInput(view, event);
}
```
