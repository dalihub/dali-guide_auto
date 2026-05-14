---
title: State Event
sidebar_label: State Event
category: input-interaction
---

# State Event

`StateEvent` describes a single View state transition and is passed to handlers connected to `StateChangedSignal`. It provides the previous and current `ViewState` together with helper methods for detecting specific transitions and optional information about the input event that caused the change.

## Table of Contents

- [Responding to State Changes](#responding-to-state-changes)
- [Detecting State Transitions](#detecting-state-transitions)
- [Accessing the Cause Event](#accessing-the-cause-event)
- [Deferred Notification Behavior](#deferred-notification-behavior)

## Responding to State Changes

Connect a handler to `StateChangedSignal()` to receive notifications when a View's state changes. The handler receives both the `View` instance and a `StateEvent` object containing transition details.

```cpp
view.StateChangedSignal().Connect(&tracker, [](View view, const StateEvent& e) {
  // Handle the state change
  ViewState previous = e.GetPrev();
  ViewState current = e.GetCurrent();
});
```

The signal fires only when the state actually changes. Setting the same state value twice results in a single notification.

```cpp
// First state change: fires signal
IntegrationView::SetState(GetImpl(view), ViewState::FOCUSED, true);

// Same state again: no signal
IntegrationView::SetState(GetImpl(view), ViewState::FOCUSED, true);
```

## Detecting State Transitions

`StateEvent` provides helper methods to check whether specific states were added, removed, or changed during the transition.

### Checking Added States

Use `Added()` to check if a state was newly added:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View view, const StateEvent& e) {
  if (e.Added(ViewState::FOCUSED)) {
    // View gained keyboard focus
  }
});
```

### Checking Removed States

Use `Removed()` to check if a state was removed:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View view, const StateEvent& e) {
  if (e.Removed(ViewState::PRESSED)) {
    // View was released
  }
});
```

### Checking Any Change

Use `Changed()` to check if a state was either added or removed:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View view, const StateEvent& e) {
  if (e.Changed(ViewState::SELECTED)) {
    // Selection state changed (either added or removed)
  }
});
```

### Direct State Comparison

For more complex logic, access the previous and current states directly:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View view, const StateEvent& e) {
  ViewState prev = e.GetPrev();
  ViewState cur = e.GetCurrent();

  if (!prev.Contains(ViewState::FOCUSED) && cur.Contains(ViewState::FOCUSED)) {
    // Equivalent to e.Added(ViewState::FOCUSED)
  }
});
```

## Accessing the Cause Event

State changes can be triggered by user input or programmatically. Use `GetCause()` to retrieve the input event that caused the transition.

```cpp
view.StateChangedSignal().Connect(&tracker, [](View view, const StateEvent& e) {
  const InputEvent& cause = e.GetCause();
  InputEventType type = e.GetInputEventType();

  if (type == InputEventType::TOUCH_EVENT) {
    const TouchEvent& touch = e.GetTouchEvent();
    // Handle touch-triggered state change
  } else if (type == InputEventType::KEY_EVENT) {
    const KeyEvent& key = e.GetKeyEvent();
    // Handle key-triggered state change
  } else if (type == InputEventType::NONE) {
    // Programmatic or system-triggered change
  }
});
```

### Gesture-Specific Accessors

For gesture-triggered state changes, use the typed accessors:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View view, const StateEvent& e) {
  InputEventType type = e.GetInputEventType();

  if (type == InputEventType::TAP_GESTURE) {
    const TapGesture& tap = e.GetTapGesture();
  } else if (type == InputEventType::LONG_PRESS_GESTURE) {
    const LongPressGesture& longPress = e.GetLongPressGesture();
  } else if (type == InputEventType::WHEEL_EVENT) {
    const WheelEvent& wheel = e.GetWheelEvent();
  }
});
```

## Deferred Notification Behavior

When a state change handler triggers another state change, the framework defers the second notification until all handlers for the first transition have completed. This ensures handlers receive consistent event ordering.

```cpp
std::vector<CallRecord> log;

view.StateChangedSignal().Connect(&tracker, [&](View v, const StateEvent& e) {
  log.push_back({"handler", e.GetPrev(), e.GetCurrent()});

  // Trigger another state change from within the handler
  if (e.Added(ViewState::FOCUSED)) {
    IntegrationView::SetState(GetImpl(v), ViewState::PRESSED, true);
  }
});

// Setting FOCUSED triggers the handler
// The handler then sets PRESSED, but that notification is deferred
// until after all handlers for FOCUSED have completed
IntegrationView::SetState(GetImpl(view), ViewState::FOCUSED, true);
```

This deferred behavior guarantees that handlers receive state transitions in a predictable order without interleaved notifications.
