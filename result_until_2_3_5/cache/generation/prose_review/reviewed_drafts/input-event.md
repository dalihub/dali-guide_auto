---
title: Input Event
sidebar_label: Input Event
category: input-interaction
---

# Input Event

`Dali::Ui::InputEvent` carries the input cause passed through dali-ui view interaction callbacks.

## Table of Contents

- [Representing an Input Cause](#representing-an-input-cause)
- [Creating a Wheel-Based Input Event](#creating-a-wheel-based-input-event)
- [Reading the Event Type](#reading-the-event-type)
- [Accessing the Typed Origin Event](#accessing-the-typed-origin-event)
- [Using a Non-Input Cause](#using-a-non-input-cause)
- [Copying and Storing Input Events](#copying-and-storing-input-events)

## Representing an Input Cause

In dali-ui application code, `Dali::Ui::InputEvent` is the app-facing handle for the event that caused a view interaction. It is used with `Dali::Ui::View` in interaction callbacks so handlers can identify both the affected view and the originating input.

`Dali::Ui::InputEvent` is a handle type. Pass it by value when matching dali-ui callback signatures, or pass it by `const` reference in helper functions that only inspect it.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>
#include <dali-ui-foundation/public-api/view.h>

namespace
{
void HandleViewInput(Dali::Ui::View view, Dali::Ui::InputEvent event)
{
  Dali::Ui::InputEventType eventType = event.GetInputEventType();

  (void)view;
  (void)eventType;
}
}
```

Use `Dali::Ui::InputEvent::GetInputEventType()` before selecting one of the typed payload accessors, because each accessor returns a specific originating DALi event object.

## Creating a Wheel-Based Input Event

When application or framework code already has a `Dali::WheelEvent`, create a dali-ui input cause with `Dali::Ui::InputEvent::New(const Dali::WheelEvent&)`.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

Dali::Ui::InputEvent MakeWheelInputEvent(const Dali::WheelEvent& wheelEvent)
{
  return Dali::Ui::InputEvent::New(wheelEvent);
}
```

The returned `Dali::Ui::InputEvent` can be stored, forwarded to helper code, or passed through dali-ui logic that expects an input cause. The same `Dali::Ui::InputEvent::New(...)` pattern is also available for `Dali::TouchEvent`, `Dali::KeyEvent`, `Dali::TapGesture`, and `Dali::LongPressGesture`.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

void ForwardWheelInput(const Dali::WheelEvent& wheelEvent)
{
  Dali::Ui::InputEvent inputEvent = Dali::Ui::InputEvent::New(wheelEvent);
  Dali::Ui::InputEventType eventType = inputEvent.GetInputEventType();

  (void)eventType;
}
```

## Reading the Event Type

`Dali::Ui::InputEvent::GetInputEventType()` returns the `Dali::Ui::InputEventType` stored in the wrapper. Use it as the routing value for code that handles different input origins.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

Dali::Ui::InputEventType ReadInputEventType(const Dali::Ui::InputEvent& inputEvent)
{
  return inputEvent.GetInputEventType();
}
```

A common pattern is to keep routing separate from payload-specific handling. Check the type first, then call a helper that uses the matching accessor.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

void HandleKnownWheelEvent(const Dali::Ui::InputEvent& inputEvent)
{
  const Dali::WheelEvent& wheelEvent = inputEvent.GetWheelEvent();

  (void)wheelEvent;
}

void RouteInputEvent(const Dali::Ui::InputEvent& inputEvent)
{
  Dali::Ui::InputEventType eventType = inputEvent.GetInputEventType();

  (void)eventType;
}
```

## Accessing the Typed Origin Event

`Dali::Ui::InputEvent` provides typed accessors for supported input origins:

- `Dali::Ui::InputEvent::GetTouchEvent()`
- `Dali::Ui::InputEvent::GetKeyEvent()`
- `Dali::Ui::InputEvent::GetTapGesture()`
- `Dali::Ui::InputEvent::GetLongPressGesture()`
- `Dali::Ui::InputEvent::GetWheelEvent()`

Call the accessor that matches the value returned by `Dali::Ui::InputEvent::GetInputEventType()`. The implementation checks that the stored `Dali::Ui::InputEventType` matches the accessor.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

void HandleKnownTouchInput(const Dali::Ui::InputEvent& inputEvent)
{
  const Dali::TouchEvent& touchEvent = inputEvent.GetTouchEvent();

  (void)touchEvent;
}

void HandleKnownKeyInput(const Dali::Ui::InputEvent& inputEvent)
{
  const Dali::KeyEvent& keyEvent = inputEvent.GetKeyEvent();

  (void)keyEvent;
}
```

Gesture origins are read in the same way. Keep each handler narrow so it only asks for the payload type it is meant to process.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

void HandleKnownTapInput(const Dali::Ui::InputEvent& inputEvent)
{
  const Dali::TapGesture& tapGesture = inputEvent.GetTapGesture();

  (void)tapGesture;
}

void HandleKnownLongPressInput(const Dali::Ui::InputEvent& inputEvent)
{
  const Dali::LongPressGesture& longPressGesture = inputEvent.GetLongPressGesture();

  (void)longPressGesture;
}
```

Wheel input uses `Dali::Ui::InputEvent::GetWheelEvent()`.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

void HandleKnownWheelInput(const Dali::Ui::InputEvent& inputEvent)
{
  const Dali::WheelEvent& wheelEvent = inputEvent.GetWheelEvent();

  (void)wheelEvent;
}
```

## Using a Non-Input Cause

Use `Dali::Ui::InputEvent::None()` when dali-ui code needs a valid input cause but the change was not triggered by a concrete touch, key, gesture, or wheel event.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

const Dali::Ui::InputEvent& ProgrammaticInputCause()
{
  return Dali::Ui::InputEvent::None();
}
```

`Dali::Ui::InputEvent::None()` returns a shared `const Dali::Ui::InputEvent&` with `Dali::Ui::InputEventType::NONE`, which is suitable for read-only propagation. Use `Dali::Ui::InputEvent::New()` instead when a separate `NONE`-type handle is needed.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

Dali::Ui::InputEventType ReadProgrammaticCauseType()
{
  const Dali::Ui::InputEvent& inputEvent = Dali::Ui::InputEvent::None();
  return inputEvent.GetInputEventType();
}
```

## Copying and Storing Input Events

`Dali::Ui::InputEvent` supports copy construction and copy assignment, so a handler can keep the most recent cause in a member or local handle.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

class LastInputStore
{
public:
  void SetLastInput(Dali::Ui::InputEvent inputEvent)
  {
    mLastInput = inputEvent;
  }

  Dali::Ui::InputEventType GetLastInputType() const
  {
    return mLastInput.GetInputEventType();
  }

private:
  Dali::Ui::InputEvent mLastInput{Dali::Ui::InputEvent::None()};
};
```

For short helper functions, prefer `const Dali::Ui::InputEvent&` to avoid an extra handle copy when you only need `Dali::Ui::InputEvent::GetInputEventType()` or a typed payload accessor.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

void InspectInputEvent(const Dali::Ui::InputEvent& inputEvent)
{
  Dali::Ui::InputEventType eventType = inputEvent.GetInputEventType();

  (void)eventType;
}
```
