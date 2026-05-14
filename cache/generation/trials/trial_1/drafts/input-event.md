---
title: Input Event
sidebar_label: Input Event
category: input-interaction
---
# Input Event

`InputEvent` is a unified wrapper type that encapsulates different user input sources—touch, key, gesture, and wheel events—into a single object passed to interaction signal handlers.

## Table of Contents

- [Understanding InputEvent](#understanding-inputevent)
- [Event Types](#event-types)
- [Creating InputEvent Objects](#creating-inputevent-objects)
- [Receiving InputEvent in Signal Handlers](#receiving-inputevent-in-signal-handlers)
- [Inspecting Event Details](#inspecting-event-details)
- [Programmatic State Changes](#programmatic-state-changes)

## Understanding InputEvent

`Dali::Ui::InputEvent` provides a type-safe wrapper around various input event types. When a user interacts with a view, the framework creates an `InputEvent` object and passes it to registered signal handlers. This allows your application to:

- Determine what kind of input triggered the interaction
- Access the original event details when needed
- Distinguish between user-initiated and programmatic state changes

The `InputEvent` class inherits from `BaseHandle` and supports copy and move semantics.

## Event Types

The `Dali::Ui::InputEventType` enumeration defines the possible event sources:

| Value | Description |
|-------|-------------|
| `InputEventType::NONE` | No concrete input event (programmatic or system-triggered) |
| `InputEventType::TOUCH_EVENT` | Touch-based interaction |
| `InputEventType::KEY_EVENT` | Keyboard input |
| `InputEventType::TAP_GESTURE` | Tap gesture recognition |
| `InputEventType::LONG_PRESS_GESTURE` | Long press gesture recognition |
| `InputEventType::WHEEL_EVENT` | Mouse wheel or scroll event |

Use `GetInputEventType()` to determine the event type before accessing specific event details:

```cpp
void OnClicked(View view, InputEvent event) {
  switch (event.GetInputEventType()) {
    case InputEventType::TOUCH_EVENT:
      // Handle touch-based click
      break;
    case InputEventType::KEY_EVENT:
      // Handle key-based click (e.g., Enter key)
      break;
    case InputEventType::TAP_GESTURE:
      // Handle gesture-based click
      break;
    default:
      break;
  }
}
```

## Creating InputEvent Objects

### Creating from Specific Event Types

`InputEvent` provides static `New()` overloads for each event type:

```cpp
// Create from touch event
InputEvent touchInput = InputEvent::New(touchEvent);

// Create from key event
InputEvent keyInput = InputEvent::New(keyEvent);

// Create from tap gesture
InputEvent tapInput = InputEvent::New(tapGesture);

// Create from long press gesture
InputEvent longPressInput = InputEvent::New(longPressGesture);

// Create from wheel event
InputEvent wheelInput = InputEvent::New(wheelEvent);
```

### Creating a NONE-type Event

For programmatic changes that do not originate from user input, create a NONE-type event:

```cpp
InputEvent programmaticEvent = InputEvent::New();
```

The returned event has `GetInputEventType() == InputEventType::NONE`.

### Using the Shared None Instance

When you need a valid `InputEvent` with NONE type without heap allocation, use the shared instance:

```cpp
const InputEvent& noneEvent = InputEvent::None();
```

This is efficient for cases where you need to pass an `InputEvent` but no actual user input occurred.

## Receiving InputEvent in Signal Handlers

### InteractiveView Signals

`InteractiveView` provides signal connection methods that pass `InputEvent` to handlers:

```cpp
// Connect to click signal
InteractiveView button = InteractiveView::New();
button.ConnectClickedSignal(this, [](View view, InputEvent event) {
  // Handle click
});

// Connect to pressed state changes
button.ConnectPressedChangedSignal(this, [](View view, bool pressed, InputEvent event) {
  if (pressed) {
    // User pressed down
  } else {
    // User released
  }
});

// Connect to long press signal
button.ConnectLongPressedSignal(this, [](View view, InputEvent event) -> bool {
  // Return true to consume the event and suppress the click
  return true;
});
```

### InteractiveTrait Signals

When using `InteractiveTrait` attached to a generic `View`, connect via the trait's signals:

```cpp
View view = View::New();
InteractiveTrait trait = view.EnsureInteractiveTrait();
trait.ClickedSignal().Connect(this, [](View view, InputEvent event) {
  // Handle click
});
```

### SelectableTrait Signals

`SelectableTrait` passes `InputEvent` in its selection changed signal:

```cpp
View view = View::New();
SelectableTrait trait = view.EnsureSelectableTrait();
trait.SelectionChangedSignal().Connect(this, [](View view, bool selected, InputEvent event) {
  if (event.GetInputEventType() == InputEventType::NONE) {
    // Selection changed programmatically
  } else {
    // Selection changed due to user interaction
  }
});
```

## Inspecting Event Details

After determining the event type, retrieve the original event to access detailed information:

```cpp
void OnClicked(View view, InputEvent event) {
  switch (event.GetInputEventType()) {
    case InputEventType::TOUCH_EVENT: {
      const TouchEvent& touch = event.GetTouchEvent();
      // Access touch details: position, time, state, etc.
      break;
    }
    case InputEventType::KEY_EVENT: {
      const KeyEvent& key = event.GetKeyEvent();
      // Access key details: key code, key string, state, etc.
      break;
    }
    case InputEventType::TAP_GESTURE: {
      const TapGesture& tap = event.GetTapGesture();
      // Access tap details: number of taps, screen position, etc.
      break;
    }
    case InputEventType::LONG_PRESS_GESTURE: {
      const LongPressGesture& longPress = event.GetLongPressGesture();
      // Access long press details
      break;
    }
    case InputEventType::WHEEL_EVENT: {
      const WheelEvent& wheel = event.GetWheelEvent();
      // Access wheel details: direction, delta, etc.
      break;
    }
    case InputEventType::NONE:
    default:
      // No user input event available
      break;
  }
}
```

## Programmatic State Changes

When a view state changes programmatically rather than through user input, the associated `InputEvent` has type `NONE`. This pattern appears in `StateEvent` passed to `StateChangedSignal` handlers:

```cpp
view.StateChangedSignal().Connect(this, [](View view, const StateEvent& e) {
  if (e.GetInputEventType() == InputEventType::NONE) {
    // State changed programmatically or by framework
  } else if (e.Added(ViewState::PRESSED)) {
    // User initiated press
    const InputEvent& cause = e.GetCause();
    // Inspect the specific input that caused the state change
  }
});
```

The `StateEvent::GetCause()` method returns the `InputEvent` that triggered the state transition, allowing you to distinguish between user actions and programmatic changes.
