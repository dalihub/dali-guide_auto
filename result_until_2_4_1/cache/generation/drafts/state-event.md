---
title: State Event
sidebar_label: State Event
category: input-interaction
---

# State Event

`Dali::Ui::StateEvent` describes one `Dali::Ui::View` state transition and gives application code the previous state, current state, and optional input cause.

## Table of Contents

- [Reacting to View State Transitions](#reacting-to-view-state-transitions)
- [Detecting Added, Removed, and Changed States](#detecting-added-removed-and-changed-states)
- [Reading the Previous and Current State Snapshots](#reading-the-previous-and-current-state-snapshots)
- [Using the Input Event Cause](#using-the-input-event-cause)
- [Keeping State Handlers Small](#keeping-state-handlers-small)

## Reacting to View State Transitions

A state event is delivered when a `Dali::Ui::View` changes state. In application code, handle the event as a short-lived callback parameter and read the transition from the `Dali::Ui::StateEvent`.

```cpp
void OnStateChanged(Dali::Ui::View view, const Dali::Ui::StateEvent& event)
{
  if(event.Added(Dali::Ui::ViewState::FOCUSED))
  {
    // The view gained focus in this transition.
  }

  if(event.Removed(Dali::Ui::ViewState::PRESSED))
  {
    // The view is no longer pressed.
  }
}
```

`Dali::Ui::StateEvent` is a handle type. It can be copied or assigned when the callback needs to pass the event to another local helper.

```cpp
void ForwardStateEvent(const Dali::Ui::StateEvent& event)
{
  Dali::Ui::StateEvent copiedEvent;
  copiedEvent = event;

  if(copiedEvent.Changed(Dali::Ui::ViewState::SELECTED))
  {
    // Selection state toggled.
  }
}
```

## Detecting Added, Removed, and Changed States

Use `Dali::Ui::StateEvent::Added`, `Dali::Ui::StateEvent::Removed`, and `Dali::Ui::StateEvent::Changed` when you care about a specific `Dali::Ui::ViewState`.

```cpp
void UpdateFocusDecoration(const Dali::Ui::StateEvent& event)
{
  if(event.Added(Dali::Ui::ViewState::FOCUSED))
  {
    ShowFocusDecoration();
  }
  else if(event.Removed(Dali::Ui::ViewState::FOCUSED))
  {
    HideFocusDecoration();
  }
}
```

`Dali::Ui::StateEvent::Changed` is useful when the same update path can handle both directions of a transition.

```cpp
void RefreshPressedVisual(const Dali::Ui::StateEvent& event)
{
  if(!event.Changed(Dali::Ui::ViewState::PRESSED))
  {
    return;
  }

  const bool pressed = event.GetCurrent().Contains(Dali::Ui::ViewState::PRESSED);
  SetPressedVisual(pressed);
}
```

For combined states, test each state that matters to the visual or behavior.

```cpp
void RefreshSelectionAndFocus(const Dali::Ui::StateEvent& event)
{
  const bool selectionChanged = event.Changed(Dali::Ui::ViewState::SELECTED);
  const bool focusChanged     = event.Changed(Dali::Ui::ViewState::FOCUSED);

  if(selectionChanged || focusChanged)
  {
    const Dali::Ui::ViewState& current = event.GetCurrent();

    SetSelectedVisual(current.Contains(Dali::Ui::ViewState::SELECTED));
    SetFocusedVisual(current.Contains(Dali::Ui::ViewState::FOCUSED));
  }
}
```

## Reading the Previous and Current State Snapshots

`Dali::Ui::StateEvent::GetPrev` returns the state before the transition. `Dali::Ui::StateEvent::GetCurrent` returns the state after the transition. These snapshots let one handler understand the whole state change, not only a single flag.

```cpp
void UpdateDisabledVisual(const Dali::Ui::StateEvent& event)
{
  const Dali::Ui::ViewState& previous = event.GetPrev();
  const Dali::Ui::ViewState& current  = event.GetCurrent();

  const bool wasDisabled = previous.Contains(Dali::Ui::ViewState::DISABLED);
  const bool isDisabled  = current.Contains(Dali::Ui::ViewState::DISABLED);

  if(wasDisabled != isDisabled)
  {
    SetDisabledVisual(isDisabled);
  }
}
```

The current state may contain more than one state at the same time. Check the `Dali::Ui::ViewState` snapshot for every state your component cares about.

```cpp
void RefreshButtonChrome(const Dali::Ui::StateEvent& event)
{
  const Dali::Ui::ViewState& current = event.GetCurrent();

  const bool focused  = current.Contains(Dali::Ui::ViewState::FOCUSED);
  const bool pressed  = current.Contains(Dali::Ui::ViewState::PRESSED);
  const bool selected = current.Contains(Dali::Ui::ViewState::SELECTED);

  ApplyButtonChrome(focused, pressed, selected);
}
```

## Using the Input Event Cause

A state transition may be caused by input, or it may be caused by programmatic or framework state changes. `Dali::Ui::StateEvent::GetInputEventType` reports which kind of input is available through the typed accessors.

```cpp
void DispatchStateCause(const Dali::Ui::StateEvent& event)
{
  switch(event.GetInputEventType())
  {
    case Dali::Ui::InputEventType::TOUCH_EVENT:
    {
      const Dali::TouchEvent& touchEvent = event.GetTouchEvent();
      HandleTouchStateChange(touchEvent);
      break;
    }

    case Dali::Ui::InputEventType::KEY_EVENT:
    {
      const Dali::KeyEvent& keyEvent = event.GetKeyEvent();
      HandleKeyStateChange(keyEvent);
      break;
    }

    case Dali::Ui::InputEventType::TAP_GESTURE:
    {
      const Dali::TapGesture& tapGesture = event.GetTapGesture();
      HandleTapStateChange(tapGesture);
      break;
    }

    case Dali::Ui::InputEventType::LONG_PRESS_GESTURE:
    {
      const Dali::LongPressGesture& longPressGesture = event.GetLongPressGesture();
      HandleLongPressStateChange(longPressGesture);
      break;
    }

    case Dali::Ui::InputEventType::WHEEL_EVENT:
    {
      const Dali::WheelEvent& wheelEvent = event.GetWheelEvent();
      HandleWheelStateChange(wheelEvent);
      break;
    }

    case Dali::Ui::InputEventType::NONE:
    case Dali::Ui::InputEventType::RESERVED:
    {
      HandleNonInputStateChange();
      break;
    }
  }
}
```

Use `Dali::Ui::StateEvent::GetCause` when you want to forward the generic input wrapper instead of branching immediately.

```cpp
void QueueStateTransition(const Dali::Ui::StateEvent& event)
{
  const Dali::Ui::InputEvent& cause = event.GetCause();

  QueueStateRecord(event.GetPrev(), event.GetCurrent(), cause);
}
```

Only call a typed getter such as `Dali::Ui::StateEvent::GetKeyEvent` after checking that `Dali::Ui::StateEvent::GetInputEventType` matches the corresponding `Dali::Ui::InputEventType`.

## Keeping State Handlers Small

State callbacks can be used to update visuals, trigger application behavior, or forward compact transition data. Keep the callback focused on the transition and move larger work into helper functions.

```cpp
void OnComponentStateChanged(Dali::Ui::View view, const Dali::Ui::StateEvent& event)
{
  if(event.Changed(Dali::Ui::ViewState::FOCUSED))
  {
    UpdateFocusDecoration(view, event.GetCurrent());
  }

  if(event.Changed(Dali::Ui::ViewState::PRESSED))
  {
    UpdatePressedFeedback(view, event.GetCurrent());
  }

  if(event.Changed(Dali::Ui::ViewState::DISABLED))
  {
    UpdateInputAvailability(view, event.GetCurrent());
  }
}
```

When one state change causes another state update, DALi dispatches state notifications as transition events. Write handlers so each call responds only to the `Dali::Ui::StateEvent` it received.

```cpp
void OnFocusableButtonStateChanged(Dali::Ui::View view, const Dali::Ui::StateEvent& event)
{
  if(event.Added(Dali::Ui::ViewState::FOCUSED))
  {
    ShowFocusDecoration(view);
  }

  if(event.Added(Dali::Ui::ViewState::PRESSED))
  {
    StartPressedAnimation(view);
  }

  if(event.Removed(Dali::Ui::ViewState::PRESSED))
  {
    StopPressedAnimation(view);
  }
}
```
