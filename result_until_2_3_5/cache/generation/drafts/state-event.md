---
title: State Event
sidebar_label: State Event
category: input-interaction
---

# State Event

`Dali::Ui::StateEvent` describes one state transition for a `Dali::Ui::View`.

## Table of Contents

- [Reacting to View State Transitions](#reacting-to-view-state-transitions)
- [Detecting Added, Removed, and Changed States](#detecting-added-removed-and-changed-states)
- [Reading the Previous and Current State](#reading-the-previous-and-current-state)
- [Using the Input Event Cause](#using-the-input-event-cause)
- [Keeping State Handling Local to the View](#keeping-state-handling-local-to-the-view)

## Reacting to View State Transitions

A `Dali::Ui::StateEvent` is delivered by the view state changed signal. In application code, treat the `Dali::Ui::View` passed to the callback as the object whose state changed, and use the `Dali::Ui::StateEvent` payload to inspect the transition.

```cpp
#include <dali-ui-foundation/public-api/state-event.h>
#include <dali-ui-foundation/public-api/view.h>
#include <dali-ui-foundation/public-api/view-state.h>

class StateAwareController : public Dali::ConnectionTracker
{
public:
  void Watch(Dali::Ui::View view)
  {
    view.StateChangedSignal().Connect(
      this,
      [this](Dali::Ui::View changedView, Dali::Ui::StateEvent event)
      {
        OnViewStateChanged(changedView, event);
      });
  }

private:
  void OnViewStateChanged(Dali::Ui::View view, Dali::Ui::StateEvent event)
  {
    if(event.Added(Dali::Ui::ViewState::FOCUSED))
    {
      ShowFocusFeedback(view);
    }

    if(event.Removed(Dali::Ui::ViewState::FOCUSED))
    {
      HideFocusFeedback(view);
    }
  }

  void ShowFocusFeedback(Dali::Ui::View view);
  void HideFocusFeedback(Dali::Ui::View view);
};
```

`Dali::Ui::StateEvent` is the owned event object for this feature. Application code normally receives it from `Dali::Ui::View::StateChangedSignal()` and then calls member functions such as `Added()`, `Removed()`, `Changed()`, `GetPrev()`, and `GetCurrent()`.

## Detecting Added, Removed, and Changed States

Use `Dali::Ui::StateEvent::Added()` when a state entered the current transition, `Dali::Ui::StateEvent::Removed()` when it left the transition, and `Dali::Ui::StateEvent::Changed()` when either direction is meaningful.

```cpp
void HandleInteractionState(Dali::Ui::View view, Dali::Ui::StateEvent event)
{
  if(event.Added(Dali::Ui::ViewState::PRESSED))
  {
    BeginPressedVisual(view);
  }

  if(event.Removed(Dali::Ui::ViewState::PRESSED))
  {
    EndPressedVisual(view);
  }

  if(event.Changed(Dali::Ui::ViewState::DISABLED))
  {
    UpdateEnabledPresentation(view, !event.GetCurrent().Contains(Dali::Ui::ViewState::DISABLED));
  }
}

void BeginPressedVisual(Dali::Ui::View view);
void EndPressedVisual(Dali::Ui::View view);
void UpdateEnabledPresentation(Dali::Ui::View view, bool enabled);
```

`Added(state)` is equivalent to checking that `state` was absent from `GetPrev()` and present in `GetCurrent()`. `Removed(state)` checks the opposite direction. `Changed(state)` is useful for state-dependent UI refresh code where the same rendering path handles both entry and exit.

## Reading the Previous and Current State

`Dali::Ui::StateEvent::GetPrev()` returns the state before the transition, and `Dali::Ui::StateEvent::GetCurrent()` returns the state after the transition. Both values are `Dali::Ui::ViewState` objects and may contain multiple active states.

```cpp
void RefreshSelectionAndFocus(Dali::Ui::View view, Dali::Ui::StateEvent event)
{
  const Dali::Ui::ViewState& previous = event.GetPrev();
  const Dali::Ui::ViewState& current  = event.GetCurrent();

  const bool wasSelected = previous.Contains(Dali::Ui::ViewState::SELECTED);
  const bool isSelected  = current.Contains(Dali::Ui::ViewState::SELECTED);

  const bool isFocused = current.Contains(Dali::Ui::ViewState::FOCUSED);

  if(wasSelected != isSelected)
  {
    ApplySelectedStyle(view, isSelected);
  }

  ApplyFocusStyle(view, isFocused);
}

void ApplySelectedStyle(Dali::Ui::View view, bool selected);
void ApplyFocusStyle(Dali::Ui::View view, bool focused);
```

Prefer `Added()`, `Removed()`, and `Changed()` for simple transition tests. Use `GetPrev()` and `GetCurrent()` when the new presentation depends on a combination of states, such as selected plus focused.

## Using the Input Event Cause

A state transition may be caused by user input, or it may be programmatic or system-triggered. `Dali::Ui::StateEvent::GetInputEventType()` reports the cause type. `Dali::Ui::StateEvent::GetCause()` returns the underlying `Dali::Ui::InputEvent`.

Only call the typed accessors when the reported `Dali::Ui::InputEventType` matches the accessor precondition.

```cpp
void HandleStateCause(Dali::Ui::View view, Dali::Ui::StateEvent event)
{
  switch(event.GetInputEventType())
  {
    case Dali::Ui::InputEventType::TOUCH_EVENT:
    {
      const Dali::TouchEvent& touchEvent = event.GetTouchEvent();
      HandleTouchDrivenState(view, touchEvent);
      break;
    }

    case Dali::Ui::InputEventType::KEY_EVENT:
    {
      const Dali::KeyEvent& keyEvent = event.GetKeyEvent();
      HandleKeyDrivenState(view, keyEvent);
      break;
    }

    case Dali::Ui::InputEventType::TAP_GESTURE:
    {
      const Dali::TapGesture& tapGesture = event.GetTapGesture();
      HandleTapDrivenState(view, tapGesture);
      break;
    }

    case Dali::Ui::InputEventType::LONG_PRESS_GESTURE:
    {
      const Dali::LongPressGesture& longPressGesture = event.GetLongPressGesture();
      HandleLongPressDrivenState(view, longPressGesture);
      break;
    }

    case Dali::Ui::InputEventType::WHEEL_EVENT:
    {
      const Dali::WheelEvent& wheelEvent = event.GetWheelEvent();
      HandleWheelDrivenState(view, wheelEvent);
      break;
    }

    case Dali::Ui::InputEventType::NONE:
    default:
    {
      HandleNonInputStateChange(view, event.GetCause());
      break;
    }
  }
}

void HandleTouchDrivenState(Dali::Ui::View view, const Dali::TouchEvent& event);
void HandleKeyDrivenState(Dali::Ui::View view, const Dali::KeyEvent& event);
void HandleTapDrivenState(Dali::Ui::View view, const Dali::TapGesture& gesture);
void HandleLongPressDrivenState(Dali::Ui::View view, const Dali::LongPressGesture& gesture);
void HandleWheelDrivenState(Dali::Ui::View view, const Dali::WheelEvent& event);
void HandleNonInputStateChange(Dali::Ui::View view, const Dali::Ui::InputEvent& cause);
```

`Dali::Ui::StateEvent::GetInputEventType()` is a convenience shortcut for the type stored in `Dali::Ui::StateEvent::GetCause()`. For programmatic or system-triggered transitions, the type is `Dali::Ui::InputEventType::NONE`.

## Keeping State Handling Local to the View

A state callback can update only the changed `Dali::Ui::View`, keeping input behavior local to the view tree. This is useful for reusable controls that need to respond to focus, pressed, selected, and disabled transitions without exposing raw actor-level state handling.

```cpp
class SelectableItem : public Dali::ConnectionTracker
{
public:
  explicit SelectableItem(Dali::Ui::View root)
  : mRoot(root)
  {
    mRoot.StateChangedSignal().Connect(
      this,
      [this](Dali::Ui::View view, Dali::Ui::StateEvent event)
      {
        if(event.Changed(Dali::Ui::ViewState::SELECTED) ||
           event.Changed(Dali::Ui::ViewState::FOCUSED) ||
           event.Changed(Dali::Ui::ViewState::DISABLED))
        {
          UpdatePresentation(view, event.GetCurrent());
        }
      });
  }

private:
  void UpdatePresentation(Dali::Ui::View view, const Dali::Ui::ViewState& state)
  {
    const bool selected = state.Contains(Dali::Ui::ViewState::SELECTED);
    const bool focused  = state.Contains(Dali::Ui::ViewState::FOCUSED);
    const bool disabled = state.Contains(Dali::Ui::ViewState::DISABLED);

    ApplyItemPresentation(view, selected, focused, disabled);
  }

  void ApplyItemPresentation(Dali::Ui::View view, bool selected, bool focused, bool disabled);

  Dali::Ui::View mRoot;
};
```

`Dali::Ui::StateEvent` is lightweight to pass through helper functions by value or by const reference. The important rule is to base application decisions on the transition payload: use `GetPrev()` for the old state, `GetCurrent()` for the new state, and the transition helpers when a single state bit is the only condition you need.
