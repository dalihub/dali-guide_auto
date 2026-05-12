---
title: State Event
sidebar_label: State Event
category: input-interaction
---

# State Event

`Dali::Ui::StateEvent` reports how a `Dali::Ui::View` state changed and, when available, which input event caused the transition.

## Table of Contents

- [Receiving View State Changes](#receiving-view-state-changes)
- [Detecting Added, Removed, and Changed States](#detecting-added-removed-and-changed-states)
- [Reading Previous and Current State Snapshots](#reading-previous-and-current-state-snapshots)
- [Inspecting the Input Cause](#inspecting-the-input-cause)
- [Storing and Forwarding State Events](#storing-and-forwarding-state-events)

## Receiving View State Changes

In a dali-ui application, use `Dali::Ui::View` as the app-facing object and handle `Dali::Ui::StateEvent` from the view state changed signal. The callback receives the `Dali::Ui::View` whose state changed and the `Dali::Ui::StateEvent` describing that transition.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

class StateObserver : public ConnectionTracker
{
public:
  void Observe(View view)
  {
    view.StateChangedSignal().Connect(this, &StateObserver::OnStateChanged);
  }

private:
  void OnStateChanged(View view, StateEvent event)
  {
    if(event.Added(ViewState::FOCUSED))
    {
      // The view has just become focused.
      mFocusedView = view;
    }

    if(event.Removed(ViewState::FOCUSED))
    {
      // The same transition removed focus from the view.
      mFocusedView = View();
    }
  }

  View mFocusedView;
};
```

`Dali::Ui::StateEvent` is owned content for this feature: application code normally consumes it inside the state callback rather than constructing it directly. `Dali::Ui::View` and `Dali::Ui::ViewState` provide the application object and state values used by the event.

## Detecting Added, Removed, and Changed States

Use `Dali::Ui::StateEvent::Added`, `Dali::Ui::StateEvent::Removed`, and `Dali::Ui::StateEvent::Changed` when the handler only cares about a particular `Dali::Ui::ViewState`.

```cpp
void UpdateInteractionState(Dali::Ui::View view, Dali::Ui::StateEvent event)
{
  if(event.Added(Dali::Ui::ViewState::PRESSED))
  {
    // Start pressed visual feedback for this View.
  }

  if(event.Removed(Dali::Ui::ViewState::PRESSED))
  {
    // End pressed visual feedback for this View.
  }

  if(event.Changed(Dali::Ui::ViewState::SELECTED))
  {
    const bool selected = event.GetCurrent().Contains(Dali::Ui::ViewState::SELECTED);

    if(selected)
    {
      // Apply selected presentation.
    }
    else
    {
      // Return to the non-selected presentation.
    }
  }
}
```

`Dali::Ui::StateEvent::Added(state)` is true when `state` was absent from `Dali::Ui::StateEvent::GetPrev` and present in `Dali::Ui::StateEvent::GetCurrent`. `Dali::Ui::StateEvent::Removed(state)` checks the opposite transition, and `Dali::Ui::StateEvent::Changed(state)` is true for either direction.

## Reading Previous and Current State Snapshots

`Dali::Ui::StateEvent::GetPrev` and `Dali::Ui::StateEvent::GetCurrent` return the complete state snapshots around one transition. This is useful when a view can hold multiple state bits at once, such as focused and selected.

```cpp
struct StateSnapshot
{
  bool wasFocused;
  bool isFocused;
  bool wasPressed;
  bool isPressed;
};

StateSnapshot ReadSnapshot(Dali::Ui::StateEvent event)
{
  const Dali::Ui::ViewState& previous = event.GetPrev();
  const Dali::Ui::ViewState& current  = event.GetCurrent();

  StateSnapshot snapshot;
  snapshot.wasFocused = previous.Contains(Dali::Ui::ViewState::FOCUSED);
  snapshot.isFocused  = current.Contains(Dali::Ui::ViewState::FOCUSED);
  snapshot.wasPressed = previous.Contains(Dali::Ui::ViewState::PRESSED);
  snapshot.isPressed  = current.Contains(Dali::Ui::ViewState::PRESSED);
  return snapshot;
}
```

The returned `Dali::Ui::ViewState` values describe one state transition. Keep derived application state if it must live beyond the callback.

## Inspecting the Input Cause

`Dali::Ui::StateEvent::GetCause` returns the input event associated with the transition. `Dali::Ui::StateEvent::GetInputEventType` is the convenient way to branch before reading a typed event with `Dali::Ui::StateEvent::GetTouchEvent`, `Dali::Ui::StateEvent::GetKeyEvent`, `Dali::Ui::StateEvent::GetTapGesture`, `Dali::Ui::StateEvent::GetLongPressGesture`, or `Dali::Ui::StateEvent::GetWheelEvent`.

```cpp
void HandleStateCause(Dali::Ui::View view, Dali::Ui::StateEvent event)
{
  switch(event.GetInputEventType())
  {
    case Dali::Ui::InputEventType::TOUCH_EVENT:
    {
      const Dali::TouchEvent& touchEvent = event.GetTouchEvent();
      (void)touchEvent;
      break;
    }

    case Dali::Ui::InputEventType::KEY_EVENT:
    {
      const Dali::KeyEvent& keyEvent = event.GetKeyEvent();
      (void)keyEvent;
      break;
    }

    case Dali::Ui::InputEventType::TAP_GESTURE:
    {
      const Dali::TapGesture& tapGesture = event.GetTapGesture();
      (void)tapGesture;
      break;
    }

    case Dali::Ui::InputEventType::LONG_PRESS_GESTURE:
    {
      const Dali::LongPressGesture& longPressGesture = event.GetLongPressGesture();
      (void)longPressGesture;
      break;
    }

    case Dali::Ui::InputEventType::WHEEL_EVENT:
    {
      const Dali::WheelEvent& wheelEvent = event.GetWheelEvent();
      (void)wheelEvent;
      break;
    }

    case Dali::Ui::InputEventType::NONE:
    default:
    {
      // Programmatic or framework-triggered state change.
      break;
    }
  }

  (void)view;
}
```

Call the typed getter only after checking the matching `Dali::Ui::InputEventType`. For example, use `Dali::Ui::StateEvent::GetTapGesture` only when `Dali::Ui::StateEvent::GetInputEventType` returned `Dali::Ui::InputEventType::TAP_GESTURE`.

## Storing and Forwarding State Events

`Dali::Ui::StateEvent` is a handle type. It has a default constructor, copy assignment with `Dali::Ui::StateEvent::operator=`, and a default destructor. Application code can copy the event handle when forwarding callback data to another object.

```cpp
class LastStateTransition
{
public:
  void OnStateChanged(Dali::Ui::View view, Dali::Ui::StateEvent event)
  {
    mView  = view;
    mEvent = event;

    if(mEvent.Changed(Dali::Ui::ViewState::FOCUSED))
    {
      mHadFocusTransition = true;
    }
  }

  bool HadFocusTransition() const
  {
    return mHadFocusTransition;
  }

private:
  Dali::Ui::View       mView;
  Dali::Ui::StateEvent mEvent;
  bool                 mHadFocusTransition{false};
};
```

Use `Dali::Ui::StateEvent::GetPrev`, `Dali::Ui::StateEvent::GetCurrent`, and the transition helpers to extract the information your application needs. For new application code, keep the examples view-based and avoid raw actor state handling.
