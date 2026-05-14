---
title: Input Event
sidebar_label: Input Event
category: input-interaction
---

# Input Event

`Dali::Ui::InputEvent` provides a unified wrapper for different input types including touch events, key events, and gestures. It enables signal handlers to receive and inspect the originating input source through a single interface.

## Table of Contents

- [Event Types](#event-types)
- [Creating Input Events](#creating-input-events)
- [Inspecting Event Contents](#inspecting-event-contents)
- [Using InputEvent in Signal Handlers](#using-inputevent-in-signal-handlers)

## Event Types

`InputEvent` wraps several input categories defined by the `Dali::Ui::InputEventType` enum:

| Type | Description |
|------|-------------|
| `InputEventType::NONE` | No concrete input event; used for programmatic state changes or framework-triggered lifecycle events |
| `InputEventType::TOUCH_EVENT` | Originates from a `TouchEvent` |
| `InputEventType::KEY_EVENT` | Originates from a `KeyEvent` |
| `InputEventType::TAP_GESTURE` | Originates from a `TapGesture` |
| `InputEventType::LONG_PRESS_GESTURE` | Originates from a `LongPressGesture` |
| `InputEventType::WHEEL_EVENT` | Originates from a `WheelEvent` |

Use `GetInputEventType()` to determine the wrapped event type before accessing specific event data.

## Creating Input Events

### Creating from Source Events

`InputEvent` provides static `New()` overloads for each supported source type:

```cpp
// Create from touch event
Dali::TouchEvent touchEvent = /* ... */;
Dali::Ui::InputEvent inputEvent = Dali::Ui::InputEvent::New(touchEvent);

// Create from key event
Dali::KeyEvent keyEvent = /* ... */;
Dali::Ui::InputEvent inputEvent = Dali::Ui::InputEvent::New(keyEvent);

// Create from tap gesture
Dali::TapGesture tapGesture = /* ... */;
Dali::Ui::InputEvent inputEvent = Dali::Ui::InputEvent::New(tapGesture);

// Create from long press gesture
Dali::LongPressGesture longPressGesture = /* ... */;
Dali::Ui::InputEvent inputEvent = Dali::Ui::InputEvent::New(longPressGesture);

// Create from wheel event
Dali::WheelEvent wheelEvent = /* ... */;
Dali::Ui::InputEvent inputEvent = Dali::Ui::InputEvent::New(wheelEvent);
```

### Using the Shared NONE Instance

For programmatic state changes that do not originate from user input, use the shared `None()` instance to avoid per-call heap allocation:

```cpp
// Efficient: returns a shared static instance
const Dali::Ui::InputEvent& noInput = Dali::Ui::InputEvent::None();
```

This is useful when triggering state changes from code rather than user interaction.

## Inspecting Event Contents

### Determining Event Type

Call `GetInputEventType()` to check what kind of event is wrapped:

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
            // Process long press gesture
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

### Retrieving Source Events

Each getter method returns a const reference to the wrapped event. Only call the getter that matches the current event type:

```cpp
Dali::Ui::InputEvent inputEvent = Dali::Ui::InputEvent::New(keyEvent);

// Correct: event type is KEY_EVENT
const Dali::KeyEvent& key = inputEvent.GetKeyEvent();

// Incorrect: would assert in debug builds
// const Dali::TouchEvent& touch = inputEvent.GetTouchEvent();
```

## Using InputEvent in Signal Handlers

### ClickedSignal Handler

The `ClickedSignal` on `InteractiveTrait` passes an `InputEvent` to the handler:

```cpp
class MyController
{
public:
    void SetupButton(Dali::Ui::View button)
    {
        button.ConnectClickedSignal(this, &MyController::OnButtonClicked);
    }

    void OnButtonClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
    {
        if (event.GetInputEventType() == Dali::Ui::InputEventType::TAP_GESTURE)
        {
            const Dali::TapGesture& tap = event.GetTapGesture();
            // Access tap details if needed
        }
        // Handle the click
    }
};
```

### PressedChangedSignal Handler

The `PressedChangedSignal` provides the pressed state and the causing `InputEvent`:

```cpp
void OnPressedChanged(Dali::Ui::View view, bool pressed, Dali::Ui::InputEvent event)
{
    if (pressed)
    {
        // Visual feedback for press state
        view.SetBackgroundColor(Dali::Ui::UiColor(0xCCCCCC));
    }
    else
    {
        // Restore normal appearance
        view.SetBackgroundColor(Dali::Ui::UiColor(0xFFFFFF));
    }
}

// Connection
view.ConnectPressedChangedSignal(this, &OnPressedChanged);
```

### LongPressedSignal Handler

The `LongPressedSignal` handler returns a boolean to control whether the click event should be suppressed:

```cpp
bool OnLongPressed(Dali::Ui::View view, Dali::Ui::InputEvent event)
{
    // Return true to consume the event and prevent ClickedSignal
    // Return false to allow ClickedSignal to fire afterward
    return true;
}

// Connection
view.ConnectLongPressedSignal(this, &OnLongPressed);
```

### Lambda Handlers

Lambda functions can also receive `InputEvent`:

```cpp
view.ConnectClickedSignal(this, [](Dali::Ui::View v, Dali::Ui::InputEvent e) {
    // Handle click, optionally inspect e for input details
});
```

For cases where the event details are not needed, the parameter can be left unnamed:

```cpp
view.ConnectClickedSignal(this, [](Dali::Ui::View v, Dali::Ui::InputEvent) {
    // Handle click without inspecting the event
});
```
