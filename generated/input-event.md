---
title: Input Event
sidebar_label: Input Event
category: input-interaction
---

# Input Event

`Dali::Ui::InputEvent` carries the input cause passed through dali-ui interaction callbacks and state-change flows.

## Table of Contents

- [InputEvent in a dali-ui app](#inputevent-in-a-dali-ui-app)
- [Routing by input event type](#routing-by-input-event-type)
- [Reading the typed origin event](#reading-the-typed-origin-event)
- [Representing non-input changes](#representing-non-input-changes)
- [Creating an InputEvent from a wheel event](#creating-an-inputevent-from-a-wheel-event)

## InputEvent in a dali-ui app

In dali-ui application code, treat `Dali::Ui::InputEvent` as the app-facing input cause object that accompanies a `Dali::Ui::View` interaction. It is a handle type: copying or assigning an `InputEvent` keeps another handle to the same input-event object.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>
#include <dali-ui-foundation/public-api/input-event-type.h>

using namespace Dali;
using namespace Dali::Ui;

class InputHandler
{
public:
  bool OnClicked(View view, InputEvent event)
  {
    mLastClickedView = view;
    mLastInputEvent = event;

    return true;
  }

private:
  View mLastClickedView;
  InputEvent mLastInputEvent;
};
```

Use `Dali::Ui::InputEvent::GetInputEventType()` before reading a typed origin event. The typed accessors, such as `Dali::Ui::InputEvent::GetTapGesture()` and `Dali::Ui::InputEvent::GetWheelEvent()`, are intended for the matching `Dali::Ui::InputEventType`.

```cpp
using namespace Dali;
using namespace Dali::Ui;

bool HandleInput(View view, InputEvent event)
{
  const InputEventType type = event.GetInputEventType();

  if(type == InputEventType::TAP_GESTURE)
  {
    const TapGesture& tap = event.GetTapGesture();
    (void)view;
    (void)tap;
    return true;
  }

  return false;
}
```

## Routing by input event type

`Dali::Ui::InputEvent::GetInputEventType()` identifies which origin event is stored in the `Dali::Ui::InputEvent`. The public event types include `Dali::Ui::InputEventType::NONE`, `Dali::Ui::InputEventType::TOUCH_EVENT`, `Dali::Ui::InputEventType::KEY_EVENT`, `Dali::Ui::InputEventType::TAP_GESTURE`, `Dali::Ui::InputEventType::LONG_PRESS_GESTURE`, and `Dali::Ui::InputEventType::WHEEL_EVENT`.

A common pattern is to keep view-level behavior in the callback and branch only when the cause matters.

```cpp
using namespace Dali;
using namespace Dali::Ui;

bool DispatchInputForView(View view, InputEvent event)
{
  switch(event.GetInputEventType())
  {
    case InputEventType::TOUCH_EVENT:
    {
      const TouchEvent& touch = event.GetTouchEvent();
      (void)view;
      (void)touch;
      return true;
    }

    case InputEventType::KEY_EVENT:
    {
      const KeyEvent& key = event.GetKeyEvent();
      (void)view;
      (void)key;
      return true;
    }

    case InputEventType::TAP_GESTURE:
    {
      const TapGesture& tap = event.GetTapGesture();
      (void)view;
      (void)tap;
      return true;
    }

    case InputEventType::LONG_PRESS_GESTURE:
    {
      const LongPressGesture& longPress = event.GetLongPressGesture();
      (void)view;
      (void)longPress;
      return true;
    }

    case InputEventType::WHEEL_EVENT:
    {
      const WheelEvent& wheel = event.GetWheelEvent();
      (void)view;
      (void)wheel;
      return true;
    }

    case InputEventType::NONE:
    case InputEventType::RESERVED:
    default:
    {
      return false;
    }
  }
}
```

## Reading the typed origin event

`Dali::Ui::InputEvent` stores one typed origin event. Read it with the accessor that matches `Dali::Ui::InputEvent::GetInputEventType()`:

- `Dali::Ui::InputEvent::GetTouchEvent()` for `Dali::Ui::InputEventType::TOUCH_EVENT`
- `Dali::Ui::InputEvent::GetKeyEvent()` for `Dali::Ui::InputEventType::KEY_EVENT`
- `Dali::Ui::InputEvent::GetTapGesture()` for `Dali::Ui::InputEventType::TAP_GESTURE`
- `Dali::Ui::InputEvent::GetLongPressGesture()` for `Dali::Ui::InputEventType::LONG_PRESS_GESTURE`
- `Dali::Ui::InputEvent::GetWheelEvent()` for `Dali::Ui::InputEventType::WHEEL_EVENT`

Keep typed event handling small and local. If a helper only supports one event kind, make that contract explicit at the call site by checking the type first.

```cpp
using namespace Dali;
using namespace Dali::Ui;

bool HandleLongPressOnly(View view, InputEvent event)
{
  if(event.GetInputEventType() != InputEventType::LONG_PRESS_GESTURE)
  {
    return false;
  }

  const LongPressGesture& longPress = event.GetLongPressGesture();

  (void)view;
  (void)longPress;
  return true;
}
```

For a click-style interaction, tap, key, touch, or other causes may be represented depending on how the interaction was produced. Code that does not need the detailed origin can accept `Dali::Ui::InputEvent` and ignore the typed payload.

```cpp
using namespace Dali;
using namespace Dali::Ui;

bool Activate(View view, InputEvent event)
{
  mActivatedView = view;
  mActivationInputType = event.GetInputEventType();

  return true;
}

View mActivatedView;
InputEventType mActivationInputType{InputEventType::NONE};
```

## Representing non-input changes

Use `Dali::Ui::InputEvent::None()` when code needs a valid `Dali::Ui::InputEvent` reference for a change that did not originate from concrete user input. The returned event has `Dali::Ui::InputEventType::NONE`.

```cpp
using namespace Dali;
using namespace Dali::Ui;

InputEventType ReadCauseType()
{
  const InputEvent& cause = InputEvent::None();
  return cause.GetInputEventType();
}
```

Use `Dali::Ui::InputEvent::New()` when a new `Dali::Ui::InputEvent` handle with `Dali::Ui::InputEventType::NONE` is needed.

```cpp
using namespace Dali;
using namespace Dali::Ui;

InputEvent MakeProgrammaticCause()
{
  InputEvent cause = InputEvent::New();

  if(cause.GetInputEventType() == InputEventType::NONE)
  {
    return cause;
  }

  return InputEvent::None();
}
```

## Creating an InputEvent from a wheel event

When a `Dali::WheelEvent` is already available, wrap it with `Dali::Ui::InputEvent::New(const WheelEvent&)`. The resulting `Dali::Ui::InputEvent` reports `Dali::Ui::InputEventType::WHEEL_EVENT` and exposes the original wheel data through `Dali::Ui::InputEvent::GetWheelEvent()`.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>
#include <dali-ui-foundation/public-api/input-event-type.h>

using namespace Dali;
using namespace Dali::Ui;

InputEvent WrapWheelEvent(const WheelEvent& wheelEvent)
{
  InputEvent event = InputEvent::New(wheelEvent);
  return event;
}

bool HandleWrappedWheel(View view, const WheelEvent& wheelEvent)
{
  InputEvent event = InputEvent::New(wheelEvent);

  if(event.GetInputEventType() != InputEventType::WHEEL_EVENT)
  {
    return false;
  }

  const WheelEvent& origin = event.GetWheelEvent();

  (void)view;
  (void)origin;
  return true;
}
```

`Dali::Ui::InputEvent` also supports normal handle assignment. This is useful when storing the latest input cause for later app-level decisions.

```cpp
using namespace Dali;
using namespace Dali::Ui;

class LastInputStore
{
public:
  void Store(InputEvent event)
  {
    mLastEvent = event;
  }

  InputEventType LastType() const
  {
    return mLastEvent ? mLastEvent.GetInputEventType() : InputEventType::NONE;
  }

private:
  InputEvent mLastEvent;
};
```
