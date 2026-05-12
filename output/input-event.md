---
title: Input Event
sidebar_label: Input Event
category: uncategorized
---

# Input Event

`InputEvent` is a unified wrapper type that encapsulates various user input sources—touch, key, gestures, and wheel events—into a single object passed to interaction signal handlers in dali-ui applications.

## Table of Contents

- [Understanding InputEvent](#understanding-inputevent)
- [Event Types and Detection](#event-types-and-detection)
- [Retrieving the Original Event](#retrieving-the-original-event)
- [InputEvent in Signal Handlers](#inputevent-in-signal-handlers)
- [Programmatic State Changes with None](#programmatic-state-changes-with-none)

## Understanding InputEvent

`Dali::Ui::InputEvent` provides a type-safe wrapper around different input sources. Rather than exposing raw `TouchEvent`, `KeyEvent`, or gesture objects directly in every signal, dali-ui uses `InputEvent` to carry the originating input when available and indicate programmatic changes when not.

The class inherits from `Dali::BaseHandle` and follows the handle-body pattern common in DALi. An uninitialized `InputEvent` acts as an empty handle, while valid instances are created through static factory methods or provided by the framework in signal callbacks.

```cpp
// InputEvent is passed to signal handlers
void OnButtonClicked(View view, InputEvent event)
{
  // Check what triggered this click
  InputEventType type = event.GetInputEventType();
  if(type == InputEventType::TOUCH_EVENT)
  {
    // User tapped the button
  }
  else if(type == InputEventType::KEY_EVENT)
  {
    // User activated via keyboard
  }
}
```

## Event Types and Detection

The `InputEventType` enumeration identifies the source of an input event. Use `GetInputEventType()` to determine the origin before accessing the specific event data.

| Value | Description |
|-------|-------------|
| `InputEventType::NONE` | No concrete input; programmatic or framework-triggered change |
| `InputEventType::TOUCH_EVENT` | Originated from a touch interaction |
| `InputEventType::KEY_EVENT` | Originated from a keyboard key press |
| `InputEventType::TAP_GESTURE` | Originated from a tap gesture recognizer |
| `InputEventType::LONG_PRESS_GESTURE` | Originated from a long-press gesture recognizer |
| `InputEventType::WHEEL_EVENT` | Originated from a mouse wheel or scroll event |

```cpp
void HandleInputEvent(View view, InputEvent event)
{
  switch(event.GetInputEventType())
  {
    case InputEventType::TOUCH_EVENT:
      // Handle touch-based interaction
      break;

    case InputEventType::KEY_EVENT:
      // Handle keyboard-based interaction
      break;

    case InputEventType::TAP_GESTURE:
      // Handle tap gesture
      break;

    case InputEventType::LONG_PRESS_GESTURE:
      // Handle long press gesture
      break;

    case InputEventType::WHEEL_EVENT:
      // Handle scroll wheel
      break;

    case InputEventType::NONE:
      // Programmatic change, not from user input
      break;
  }
}
```

## Retrieving the Original Event

After determining the event type, call the appropriate getter to access the original event data. Each getter returns a const reference to the underlying DALi event object.

```cpp
void ProcessEvent(View view, InputEvent event)
{
  InputEventType type = event.GetInputEventType();

  if(type == InputEventType::TOUCH_EVENT)
  {
    const TouchEvent& touch = event.GetTouchEvent();
    // Access touch point data, time, etc.
  }
  else if(type == InputEventType::KEY_EVENT)
  {
    const KeyEvent& key = event.GetKeyEvent();
    // Access key code, modifier state, etc.
  }
  else if(type == InputEventType::TAP_GESTURE)
  {
    const TapGesture& tap = event.GetTapGesture();
    // Access tap location, number of taps, etc.
  }
  else if(type == InputEventType::LONG_PRESS_GESTURE)
  {
    const LongPressGesture& longPress = event.GetLongPressGesture();
    // Access long press state and location
  }
  else if(type == InputEventType::WHEEL_EVENT)
  {
    const WheelEvent& wheel = event.GetWheelEvent();
    // Access scroll direction and delta
  }
}
```

Calling a getter for the wrong event type results in undefined behavior. Always verify the type with `GetInputEventType()` before accessing specific event data.

## InputEvent in Signal Handlers

`InputEvent` is passed to signal handlers throughout the dali-ui interaction system. The most common usage is with `InteractiveTrait` signals.

### Clicked Signal

The `ClickedSignal` provides the view that was clicked and the `InputEvent` that triggered the click.

```cpp
class MyController : public ConnectionTracker
{
public:
  void SetupButton()
  {
    InteractiveView button = InteractiveView::New();
    button.ConnectClickedSignal(this, &MyController::OnButtonClicked);
  }

  void OnButtonClicked(View view, InputEvent event)
  {
    // Respond to the click
    if(event.GetInputEventType() == InputEventType::KEY_EVENT)
    {
      // Keyboard activation - might want different feedback
    }
    else
    {
      // Touch/tap activation
    }
  }
};
```

### Pressed Changed Signal

The `PressedChangedSignal` reports press state transitions with the triggering `InputEvent`.

```cpp
void SetupPressHandling(InteractiveView view)
{
  view.ConnectPressedChangedSignal(
    this,
    [](View view, bool isPressed, InputEvent event) {
      if(isPressed)
      {
        // Visual feedback for press start
        view.SetScale(0.95f, 0.95f);
      }
      else
      {
        // Visual feedback for press end
        view.SetScale(1.0f, 1.0f);
      }
    }
  );
}
```

### Long Pressed Signal

The `LongPressedSignal` handler receives an `InputEvent` wrapping a `LongPressGesture`. Return `true` to consume the event and prevent the subsequent `ClickedSignal` emission.

```cpp
void SetupLongPress(InteractiveView view)
{
  view.ConnectLongPressedSignal(
    this,
    [](View view, InputEvent event) -> bool {
      // Show context menu on long press
      ShowContextMenu(view);

      // Return true to consume and suppress click
      return true;
    }
  );
}
```

### State Changed Signal

`View::StateChangedSignal` provides a `StateEvent` object that contains an `InputEvent` indicating what caused the state transition.

```cpp
void SetupStateHandling(View view)
{
  view.StateChangedSignal().Connect(
    this,
    [](View view, const StateEvent& stateEvent) {
      // Check if focus was gained
      if(stateEvent.Added(ViewState::FOCUSED))
      {
        // Determine what caused the focus change
        InputEventType cause = stateEvent.GetInputEventType();
        if(cause == InputEventType::NONE)
        {
          // Programmatic focus change
        }
        else if(cause == InputEventType::KEY_EVENT)
        {
          // Focus moved via keyboard navigation
        }
      }
    }
  );
}
```

## Programmatic State Changes with None

When state changes occur without direct user input—such as programmatic API calls or framework-triggered lifecycle events—the associated `InputEvent` has type `NONE`.

### Using the Shared None Instance

`InputEvent::None()` returns a shared instance representing a non-input change. Use this when emitting signals for programmatic state modifications.

```cpp
void SetSelectedProgrammatically(View view)
{
  // When triggering state changes from code, use None()
  // to indicate this wasn't from user input
  const InputEvent& noUserInput = InputEvent::None();
  // Pass to internal state change handlers
}
```

### Creating New None Events

`InputEvent::New()` creates a new `InputEvent` with type `NONE`. Use this when you need a distinct instance rather than the shared singleton.

```cpp
// Create a new NONE-type event (heap allocated)
InputEvent programmaticEvent = InputEvent::New();

// Or use the shared instance (no allocation)
const InputEvent& sharedNone = InputEvent::None();
```

### Detecting Programmatic Changes

In signal handlers, check for `NONE` type to distinguish user input from programmatic changes.

```cpp
void OnStateChanged(View view, const StateEvent& stateEvent)
{
  const InputEvent& cause = stateEvent.GetCause();

  if(cause.GetInputEventType() == InputEventType::NONE)
  {
    // State changed programmatically or by framework
    // Skip user-feedback animations
  }
  else
  {
    // State changed by user interaction
    // Apply appropriate feedback
  }
}