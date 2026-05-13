---
title: Input Event
sidebar_label: Input Event
category: uncategorized
---

# Input Event

`Dali::Ui::InputEvent` provides a unified wrapper for different input event types in dali-ui applications. It encapsulates touch events, key events, tap gestures, long-press gestures, and wheel events into a single type that can be passed through signal handlers and state change callbacks.

## Table of Contents

- [Understanding InputEvent](#understanding-inputevent)
- [Creating InputEvent Instances](#creating-inputevent-instances)
- [Inspecting Event Type and Origin](#inspecting-event-type-and-origin)
- [Using InputEvent in Signal Handlers](#using-inputevent-in-signal-handlers)
- [NONE Events for Non-Input Changes](#none-events-for-non-input-changes)

## Understanding InputEvent

`Dali::Ui::InputEvent` is a handle class that wraps one of several underlying event types. When a user interacts with a view through touch, keyboard, or mouse wheel, the framework creates an `InputEvent` that carries the original event data. This allows signal handlers and state change callbacks to receive a consistent type regardless of the input source.

The `InputEventType` enum defines the possible event categories:

| Value | Description |
|-------|-------------|
| `InputEventType::NONE` | No concrete input event (API call or lifecycle change) |
| `InputEventType::TOUCH_EVENT` | Touch-based input |
| `InputEventType::KEY_EVENT` | Keyboard input |
| `InputEventType::TAP_GESTURE` | Tap gesture recognized |
| `InputEventType::LONG_PRESS_GESTURE` | Long-press gesture recognized |
| `InputEventType::WHEEL_EVENT` | Mouse wheel input |

## Creating InputEvent Instances

Use the static `InputEvent::New()` methods to create an `InputEvent` from a specific origin event. Each overload accepts a different event type:

```cpp
#include <dali-ui-foundation/public-api/input-event.h>
#include <dali/public-api/events/touch-event.h>
#include <dali/public-api/events/key-event.h>
#include <dali/public-api/events/tap-gesture.h>
#include <dali/public-api/events/long-press-gesture.h>
#include <dali/public-api/events/wheel-event.h>

// Create from a touch event
Dali::TouchEvent touchEvent = GetTouchEvent();
Dali::Ui::InputEvent inputFromTouch = Dali::Ui::InputEvent::New(touchEvent);

// Create from a key event
Dali::KeyEvent keyEvent = GetKeyEvent();
Dali::Ui::InputEvent inputFromKey = Dali::Ui::InputEvent::New(keyEvent);

// Create from a tap gesture
Dali::TapGesture tapGesture = GetTapGesture();
Dali::Ui::InputEvent inputFromTap = Dali::Ui::InputEvent::New(tapGesture);

// Create from a long-press gesture
Dali::LongPressGesture longPressGesture = GetLongPressGesture();
Dali::Ui::InputEvent inputFromLongPress = Dali::Ui::InputEvent::New(longPressGesture);

// Create from a wheel event
Dali::WheelEvent wheelEvent = GetWheelEvent();
Dali::Ui::InputEvent inputFromWheel = Dali::Ui::InputEvent::New(wheelEvent);
```

## Inspecting Event Type and Origin

Call `GetInputEventType()` to determine what kind of event is wrapped. Then use the appropriate getter method to retrieve the original event:

```cpp
void HandleInputEvent(Dali::Ui::InputEvent event)
{
    switch (event.GetInputEventType())
    {
        case Dali::Ui::InputEventType::TOUCH_EVENT:
        {
            const Dali::TouchEvent& touch = event.GetTouchEvent();
            // Process touch event
            break;
        }
        case Dali::Ui::InputEventType::KEY_EVENT:
        {
            const Dali::KeyEvent& key = event.GetKeyEvent();
            // Process key event
            break;
        }
        case Dali::Ui::InputEventType::TAP_GESTURE:
        {
            const Dali::TapGesture& tap = event.GetTapGesture();
            // Process tap gesture
            break;
        }
        case Dali::Ui::InputEventType::LONG_PRESS_GESTURE:
        {
            const Dali::LongPressGesture& longPress = event.GetLongPressGesture();
            // Process long-press gesture
            break;
        }
        case Dali::Ui::InputEventType::WHEEL_EVENT:
        {
            const Dali::WheelEvent& wheel = event.GetWheelEvent();
            // Process wheel event
            break;
        }
        case Dali::Ui::InputEventType::NONE:
        default:
            // No concrete input event
            break;
    }
}
```

## Using InputEvent in Signal Handlers

Interactive views emit signals that include an `InputEvent` parameter. This allows handlers to determine what triggered the interaction and access the original event details.

### Clicked Signal

The `ClickedSignal` fires when a view is clicked, either through a tap gesture or an execution key (such as Enter). The `InputEvent` indicates the source:

```cpp
class MyController
{
public:
    void SetupButton(Dali::Ui::InteractiveView button)
    {
        button.ConnectClickedSignal(this, &MyController::OnButtonClicked);
    }

    bool OnButtonClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
    {
        if (event.GetInputEventType() == Dali::Ui::InputEventType::KEY_EVENT)
        {
            const Dali::KeyEvent& key = event.GetKeyEvent();
            // Handle keyboard-triggered click
        }
        else if (event.GetInputEventType() == Dali::Ui::InputEventType::TAP_GESTURE)
        {
            const Dali::TapGesture& tap = event.GetTapGesture();
            // Handle tap-triggered click
        }
        return true;
    }
};
```

### Long-Pressed Signal

The `LongPressedSignal` fires when a long-press gesture is recognized. The `InputEvent` wraps the `LongPressGesture`:

```cpp
class MyController
{
public:
    void SetupLongPressView(Dali::Ui::InteractiveView view)
    {
        view.ConnectLongPressedSignal(this, &MyController::OnLongPressed);
    }

    bool OnLongPressed(Dali::Ui::View view, Dali::Ui::InputEvent event)
    {
        if (event.GetInputEventType() == Dali::Ui::InputEventType::LONG_PRESS_GESTURE)
        {
            const Dali::LongPressGesture& gesture = event.GetLongPressGesture();
            // Access gesture details
        }
        return true; // Consume the event
    }
};
```

### Lambda-Based Connection

For inline handlers, use lambda expressions with `ConnectClickedSignal`:

```cpp
void SetupButton(Dali::Ui::InteractiveView button)
{
    button.ConnectClickedSignal(this, [](Dali::Ui::View view, Dali::Ui::InputEvent event) -> bool {
        // Handle click, optionally inspect event type
        if (event.GetInputEventType() == Dali::Ui::InputEventType::TOUCH_EVENT)
        {
            const Dali::TouchEvent& touch = event.GetTouchEvent();
            // Access touch details
        }
        return true;
    });
}
```

## NONE Events for Non-Input Changes

When a state change occurs without direct user input—for example, through programmatic API calls or framework-triggered lifecycle events—the framework uses an `InputEvent` with type `NONE`.

### Using the Shared NONE Instance

`InputEvent::None()` returns a shared instance that avoids heap allocation on each call. Use this when you need a valid `InputEvent` representing no concrete input:

```cpp
void NotifyStateChange(Dali::Ui::View view)
{
    // State changed programmatically, not from user input
    EmitStateChange(view, Dali::Ui::InputEvent::None());
}
```

### Creating a New NONE Event

If you need a distinct `InputEvent` instance with type `NONE`, use `InputEvent::New()`:

```cpp
Dali::Ui::InputEvent noneEvent = Dali::Ui::InputEvent::New();
// noneEvent.GetInputEventType() == Dali::Ui::InputEventType::NONE
```

### Handling NONE in Callbacks

When processing `InputEvent` in callbacks, always handle the `NONE` case gracefully:

```cpp
void OnStateChanged(Dali::Ui::View view, Dali::Ui::InputEvent cause)
{
    if (cause.GetInputEventType() == Dali::Ui::InputEventType::NONE)
    {
        // State changed programmatically or via lifecycle event
    }
    else
    {
        // State changed due to user input
    }
}
