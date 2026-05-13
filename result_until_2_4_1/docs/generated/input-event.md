---
title: Input Event
sidebar_label: Input Event
category: input-interaction
---

# Input Event

`Dali::Ui::InputEvent` identifies the input or non-input cause behind dali-ui interaction and state changes.

## Table of Contents

- [What `InputEvent` Represents](#what-inputevent-represents)
- [Creating an Input Cause](#creating-an-input-cause)
- [Reading the Typed Event Payload](#reading-the-typed-event-payload)
- [Using a Non-Input Cause](#using-a-non-input-cause)
- [Passing Input Events Through View Logic](#passing-input-events-through-view-logic)

## What `InputEvent` Represents

In a dali-ui application, view interaction logic often needs to know why a state changed. `Dali::Ui::InputEvent` is the app-facing handle for that cause. It can represent a concrete input payload, such as a touch event, key event, tap gesture, long-press gesture, or wheel event, or it can represent a non-input change.

Use `GetInputEventType()` before deciding which typed getter is valid for a particular `Dali::Ui::InputEvent`.

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

`Dali::Ui::InputEvent` is a handle type. Copying or assigning it with `operator=` keeps a reference to the same event handle rather than copying each payload field manually.

## Creating an Input Cause

When code already has a concrete input object, create a `Dali::Ui::InputEvent` with the matching `InputEvent::New()` overload. For wheel input, pass the source `Dali::WheelEvent` directly.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>
#include <dali/public-api/events/wheel-event.h>

Dali::Ui::InputEvent MakeWheelCause(const Dali::WheelEvent& wheelEvent)
{
  return Dali::Ui::InputEvent::New(wheelEvent);
}
```

A created event can then be passed through your dali-ui view controller or state logic as a single cause object.

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

## Reading the Typed Event Payload

`Dali::Ui::InputEvent` stores the original event payload. Use the getter that matches the event type you are handling. For a `Dali::Ui::InputEvent` whose type is `Dali::Ui::InputEventType::WHEEL_EVENT`, use `GetWheelEvent()`.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>
#include <dali/public-api/events/wheel-event.h>

const Dali::WheelEvent& ReadWheelEvent(const Dali::Ui::InputEvent& event)
{
  return event.GetWheelEvent();
}
```

The typed getters return const references to the stored payload and assert if the stored type does not match the getter:

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

Use `GetTouchEvent()`, `GetKeyEvent()`, `GetTapGesture()`, `GetLongPressGesture()`, and `GetWheelEvent()` only on events created from the corresponding source input.

## Using a Non-Input Cause

Not every view state change comes from direct user input. Use `InputEvent::None()` when a valid shared `Dali::Ui::InputEvent` is needed but the change is programmatic or framework-triggered.

```cpp
#include <dali-ui-foundation/public-api/input-event.h>

const Dali::Ui::InputEvent& ProgrammaticCause()
{
  return Dali::Ui::InputEvent::None();
}
```

`InputEvent::None()` returns a shared const reference with `Dali::Ui::InputEventType::NONE`, so it is suitable for repeated non-input paths. Use `InputEvent::New()` with no arguments only when you need a separate `NONE` event handle.

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

## Passing Input Events Through View Logic

Keep `Dali::Ui::InputEvent` at the boundary between input handling and view state logic. Your dali-ui code can receive concrete input, wrap it once, and pass the cause through helpers that operate on `Dali::Ui::View`-based application objects.

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

This keeps the application model centered on `Dali::Ui::View` while preserving the exact input cause for interaction-specific decisions.
