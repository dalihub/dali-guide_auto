---
title: State Event
sidebar_label: State Event
category: input-interaction
---

# State Event

`StateEvent` describes a single View state transition and is passed to `StateChangedSignal` handlers. It provides the previous and current `ViewState` together with helpers for detecting specific transitions, and optional information about the input event that caused the change.

## Table of Contents

- [Responding to State Changes](#responding-to-state-changes)
- [Detecting State Transitions](#detecting-state-transitions)
- [Accessing the Cause Event](#accessing-the-cause-event)

## Responding to State Changes

When a `View` undergoes a state transition, the `StateChangedSignal` is emitted with a `StateEvent` containing the previous and current states. Connect to this signal to respond to focus, pressed, disabled, and other state changes.

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  ViewState prev = e.GetPrev();
  ViewState cur = e.GetCurrent();
  // Handle the state transition
});
```

The signal delivers the `View` that changed and the `StateEvent` describing the transition. Multiple handlers can be connected to the same view's signal, and they are invoked in registration order.

## Detecting State Transitions

`StateEvent` provides helper methods to check whether specific states were added or removed during the transition.

### Checking Added States

Use `Added()` to detect when a state becomes active:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  if (e.Added(ViewState::FOCUSED)) {
    // View gained focus
  }
  if (e.Added(ViewState::PRESSED)) {
    // View was pressed
  }
});
```

### Checking Removed States

Use `Removed()` to detect when a state is cleared:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  if (e.Removed(ViewState::FOCUSED)) {
    // View lost focus
  }
  if (e.Removed(ViewState::PRESSED)) {
    // View was released
  }
});
```

### Checking Any State Change

Use `Changed()` to detect when a specific state was either added or removed:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  if (e.Changed(ViewState::DISABLED)) {
    // Disabled state changed (added or removed)
  }
});
```

### Accessing Previous and Current States

For detailed transition logic, access the full state before and after the transition:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  const ViewState& prev = e.GetPrev();
  const ViewState& cur = e.GetCurrent();

  if (!prev.Contains(ViewState::FOCUSED) && cur.Contains(ViewState::FOCUSED)) {
    // Equivalent to e.Added(ViewState::FOCUSED)
  }
});
```

## Accessing the Cause Event

State changes can be triggered by user input or programmatically. `StateEvent` provides access to the input event that caused the transition.

### Getting the Cause Type

Use `GetInputEventType()` to determine what triggered the state change:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  InputEventType type = e.GetInputEventType();
  if (type == InputEventType::TOUCH_EVENT) {
    // State changed due to touch
  } else if (type == InputEventType::KEY_EVENT) {
    // State changed due to key press
  } else if (type == InputEventType::NONE) {
    // Programmatic or system-triggered change
  }
});
```

### Getting the Specific Event

Retrieve the underlying input event for detailed information. Each getter has a precondition: the input event type must match the requested event type.

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  if (e.GetInputEventType() == InputEventType::TOUCH_EVENT) {
    const TouchEvent& touch = e.GetTouchEvent();
    // Process touch details
  }

  if (e.GetInputEventType() == InputEventType::KEY_EVENT) {
    const KeyEvent& key = e.GetKeyEvent();
    // Process key details
  }
});
```

For gesture-triggered state changes:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  if (e.GetInputEventType() == InputEventType::TAP_GESTURE) {
    const TapGesture& tap = e.GetTapGesture();
    // Process tap gesture
  }

  if (e.GetInputEventType() == InputEventType::LONG_PRESS_GESTURE) {
    const LongPressGesture& longPress = e.GetLongPressGesture();
    // Process long press gesture
  }
});
```

For wheel events:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  if (e.GetInputEventType() == InputEventType::WHEEL_EVENT) {
    const WheelEvent& wheel = e.GetWheelEvent();
    // Process wheel event
  }
});
```

### Getting the Generic Cause

Use `GetCause()` to retrieve the input event regardless of type:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  const InputEvent& cause = e.GetCause();
  // Always valid; type is InputEventType::NONE for programmatic changes
});
```
