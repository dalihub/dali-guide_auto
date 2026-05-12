---
title: State Event
sidebar_label: State Event
category: uncategorized
---

# State Event

`StateEvent` describes a single View state transition and is passed to `StateChangedSignal` handlers. It provides the previous and current `ViewState` with helpers for detecting specific transitions, along with optional information about the input event that caused the change.

## Table of Contents

- [Responding to State Changes](#responding-to-state-changes)
- [Detecting State Transitions](#detecting-state-transitions)
- [Accessing the Cause Event](#accessing-the-cause-event)

## Responding to State Changes

Connect to `View::StateChangedSignal()` to receive notifications whenever a View's state changes. The signal delivers a `StateEvent` object containing both the previous and current state.

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  if (e.Added(ViewState::FOCUSED)) {
    // View gained keyboard focus
    UpdateVisualForFocus(v, true);
  }
});
```

The callback receives the `View` that changed and a `const StateEvent&` describing the transition. Multiple handlers can be connected to the same View's state change signal.

## Detecting State Transitions

`StateEvent` provides helper methods to check whether specific states were added, removed, or changed during the transition.

### Checking Added States

Use `Added()` to check if a state was newly added in this transition:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  if (e.Added(ViewState::FOCUSED)) {
    // Focus was not present before, but is now
  }
  if (e.Added(ViewState::PRESSED)) {
    // View was just pressed
  }
});
```

### Checking Removed States

Use `Removed()` to check if a state was cleared:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  if (e.Removed(ViewState::PRESSED)) {
    // View was released
  }
  if (e.Removed(ViewState::FOCUSED)) {
    // View lost keyboard focus
  }
});
```

### Checking Any Change

Use `Changed()` to check if a state was either added or removed:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  if (e.Changed(ViewState::DISABLED)) {
    // Disabled state toggled (added or removed)
    UpdateEnabledAppearance(v);
  }
});
```

### Accessing Previous and Current State

For more detailed inspection, access the full state before and after the transition:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  ViewState prev = e.GetPrev();
  ViewState cur = e.GetCurrent();

  // Check composite states
  if (prev.Contains(ViewState::FOCUSED) && !cur.Contains(ViewState::FOCUSED)) {
    // Focus was lost
  }

  // Check multiple states at once
  if (cur.Contains(ViewState::FOCUSED | ViewState::PRESSED)) {
    // View is both focused and pressed
  }
});
```

## Accessing the Cause Event

State changes can be triggered by user input or programmatically. `GetCause()` returns the `InputEvent` that caused the transition, or an event with `InputEventType::NONE` for programmatic changes.

### Getting the Cause Event Type

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

### Getting Specific Event Types

For convenience, `StateEvent` provides direct accessors for each event type. Call these only when the corresponding event type caused the transition:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  if (e.GetInputEventType() == InputEventType::TOUCH_EVENT) {
    const TouchEvent& touch = e.GetTouchEvent();
    // Process touch event details
  }

  if (e.GetInputEventType() == InputEventType::KEY_EVENT) {
    const KeyEvent& key = e.GetKeyEvent();
    // Process key event details
  }

  if (e.GetInputEventType() == InputEventType::TAP_GESTURE) {
    const TapGesture& tap = e.GetTapGesture();
    // Process tap gesture
  }

  if (e.GetInputEventType() == InputEventType::LONG_PRESS_GESTURE) {
    const LongPressGesture& longPress = e.GetLongPressGesture();
    // Process long press gesture
  }

  if (e.GetInputEventType() == InputEventType::WHEEL_EVENT) {
    const WheelEvent& wheel = e.GetWheelEvent();
    // Process wheel/scroll event
  }
});
```

### Using GetCause for Generic Access

When you need the cause event without knowing its specific type:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View v, const StateEvent& e) {
  const InputEvent& cause = e.GetCause();
  // Always valid; check GetInputEventType() to determine actual type
});