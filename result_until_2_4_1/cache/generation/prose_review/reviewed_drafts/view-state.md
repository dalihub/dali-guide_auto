---
title: View State
sidebar_label: View State
category: styling-theme-config
---

# View State

`Dali::Ui::ViewState` represents one or more visual and interaction states of a `Dali::Ui::View` as a compact value that can be combined, queried, and compared.

## Table of Contents

- [Representing View State](#representing-view-state)
- [Combining Built-In States](#combining-built-in-states)
- [Testing State for Styling Decisions](#testing-state-for-styling-decisions)
- [Creating Custom States](#creating-custom-states)
- [Detecting State Transitions](#detecting-state-transitions)
- [Debugging State Values](#debugging-state-values)

## Representing View State

Use `Dali::Ui::ViewState` when application code needs to reason about the state of a `Dali::Ui::View`. A default-constructed `Dali::Ui::ViewState` is the same as `Dali::Ui::ViewState::NORMAL`, which has no state bits set.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

void CheckInitialState()
{
  ViewState state;

  if(state.IsNormal())
  {
    // The state is ViewState::NORMAL.
  }

  if(state == ViewState::NORMAL)
  {
    // Default construction and ViewState::NORMAL are equivalent.
  }

  if(!static_cast<bool>(state))
  {
    // ViewState::NORMAL has no active bits.
  }
}
```

`Dali::Ui::ViewState::operator bool` is useful when you only need to know whether a state value contains any active bit. Prefer `IsNormal()` when the code is specifically checking for `Dali::Ui::ViewState::NORMAL`.

## Combining Built-In States

`Dali::Ui::ViewState` is designed as a bitmask value. Built-in states such as `Dali::Ui::ViewState::FOCUSED`, `Dali::Ui::ViewState::PRESSED`, `Dali::Ui::ViewState::DISABLED`, `Dali::Ui::ViewState::PSEUDO_DISABLED`, and `Dali::Ui::ViewState::SELECTED` can be combined with `operator+` or `operator|`.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

ViewState MakeInteractiveState(bool focused, bool pressed, bool selected)
{
  ViewState state = ViewState::NORMAL;

  if(focused)
  {
    state = state + ViewState::FOCUSED;
  }

  if(pressed)
  {
    state = state + ViewState::PRESSED;
  }

  if(selected)
  {
    state = state + ViewState::SELECTED;
  }

  return state;
}
```

Several common combinations are already provided. Use `Dali::Ui::ViewState::SELECTED_PRESSED`, `Dali::Ui::ViewState::DISABLED_SELECTED`, and `Dali::Ui::ViewState::SELECTED_FOCUSED` when those exact combinations match the view behavior you are styling.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

void UseCompositeStates()
{
  ViewState selectedPressed = ViewState::SELECTED_PRESSED;

  if(selectedPressed.Contains(ViewState::SELECTED) &&
     selectedPressed.Contains(ViewState::PRESSED))
  {
    // Apply selected-and-pressed styling.
  }

  ViewState selectedFocused = ViewState::SELECTED_FOCUSED;

  if(selectedFocused.Contains(ViewState::FOCUSED))
  {
    // Apply focused styling for a selected view.
  }
}
```

To remove a state bit, use `Dali::Ui::ViewState::operator-`.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

ViewState ClearPressed(ViewState state)
{
  return state - ViewState::PRESSED;
}
```

## Testing State for Styling Decisions

Use `Contains()` when a styling rule requires all bits in another `Dali::Ui::ViewState` to be present. Use `HasIntersectionWith()` when any overlap is enough. Because `Dali::Ui::ViewState::NORMAL` has no bits, `Contains(Dali::Ui::ViewState::NORMAL)` is true for every state; use `IsNormal()` when you need to test that no state bits are active.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

enum class VisualRole
{
  Normal,
  Focused,
  Pressed,
  Disabled,
  SelectedPressed
};

VisualRole ChooseVisualRole(ViewState state)
{
  if(state.IsAnyDisabled())
  {
    return VisualRole::Disabled;
  }

  if(state.Contains(ViewState::SELECTED_PRESSED))
  {
    return VisualRole::SelectedPressed;
  }

  if(state.Contains(ViewState::PRESSED))
  {
    return VisualRole::Pressed;
  }

  if(state.Contains(ViewState::FOCUSED))
  {
    return VisualRole::Focused;
  }

  return VisualRole::Normal;
}
```

`Dali::Ui::ViewState::IsAnyDisabled()` checks both `Dali::Ui::ViewState::DISABLED` and `Dali::Ui::ViewState::PSEUDO_DISABLED`, which is useful when a theme wants both disabled forms to share a base visual treatment.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

bool ShouldUseDimmedPalette(ViewState state)
{
  return state.IsAnyDisabled();
}
```

For mask-style checks, `HasIntersectionWith()` keeps the condition concise.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

bool NeedsAttentionStyle(ViewState state)
{
  const ViewState attentionMask = ViewState::FOCUSED + ViewState::PRESSED;
  return state.HasIntersectionWith(attentionMask);
}
```

## Creating Custom States

Use `Dali::Ui::ViewState::Create` when an application or component needs a named state beyond the built-in set. Calling `Create` again with the same name returns the same state value, so custom states can be declared once and reused in styling logic.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

ViewState LoadingState()
{
  static const ViewState loading = ViewState::Create("Loading");
  return loading;
}

ViewState MakeLoadingSelectionState()
{
  return LoadingState() + ViewState::SELECTED;
}
```

The names `"Normal"` and `"All"` are reserved. `Dali::Ui::ViewState::Create("Normal")` returns `Dali::Ui::ViewState::NORMAL`, and `Dali::Ui::ViewState::Create("All")` returns `Dali::Ui::ViewState::ALL`.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

void CheckReservedNames()
{
  ViewState normal = ViewState::Create("Normal");
  ViewState all    = ViewState::Create("All");

  if(normal == ViewState::NORMAL)
  {
    // Reserved normal state.
  }

  if(all == ViewState::ALL)
  {
    // Reserved all-states mask.
  }
}
```

## Detecting State Transitions

When application logic has a previous and current `Dali::Ui::ViewState`, use the transition helpers on the state you care about. `WasAdded()` detects a bit that is absent in the previous state and present in the current state. `WasRemoved()` detects the reverse. `WasChanged()` detects either direction for a single state or an exact composite mask.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

void UpdatePressedVisual(ViewState previous, ViewState current)
{
  if(ViewState::PRESSED.WasAdded(previous, current))
  {
    // Start pressed visual feedback.
  }

  if(ViewState::PRESSED.WasRemoved(previous, current))
  {
    // End pressed visual feedback.
  }
}
```

In app code, `Dali::Ui::View::StateChangedSignal()` provides the previous and current state through `Dali::Ui::StateEvent`. Use the event helpers when you are handling a view callback directly.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/view.h>

using Dali::ConnectionTracker;
using Dali::Ui::StateEvent;
using Dali::Ui::View;
using Dali::Ui::ViewState;

void ConnectPressedStateHandler(View view, ConnectionTracker& tracker)
{
  view.StateChangedSignal().Connect(&tracker, [](View, const StateEvent& event) {
    if(event.Added(ViewState::PRESSED))
    {
      // Start pressed visual feedback.
    }

    if(event.Removed(ViewState::PRESSED))
    {
      // End pressed visual feedback.
    }
  });
}
```

For a composite watch mask, use `AnyChanged()`. It returns true when any bit in the receiver changed between the previous and current values.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

bool InteractionVisualNeedsRefresh(ViewState previous, ViewState current)
{
  const ViewState interactionMask = ViewState::FOCUSED +
                                    ViewState::PRESSED +
                                    ViewState::SELECTED;

  return interactionMask.AnyChanged(previous, current);
}
```

`WasChanged()` is best for a single state such as `Dali::Ui::ViewState::FOCUSED`; `AnyChanged()` is better when the receiver is a mask made from several states.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

bool FocusOrPressChanged(ViewState previous, ViewState current)
{
  const ViewState focusOrPress = ViewState::FOCUSED + ViewState::PRESSED;
  return focusOrPress.AnyChanged(previous, current);
}
```

## Debugging State Values

Use `ToString()` for readable diagnostics. `Dali::Ui::ViewState::NORMAL` formats as `"Normal"`, `Dali::Ui::ViewState::ALL` formats as `"All"`, and composite states include the registered state names. Do not depend on the order of names in a composite string.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

auto DescribeState(ViewState state)
{
  return state.ToString();
}
```

Operators such as `operator&`, `operator^`, and `operator~` are useful for diagnostics and mask calculations when you need more direct bitmask behavior.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

ViewState ChangedBits(ViewState previous, ViewState current)
{
  return previous ^ current;
}

ViewState InteractionBits(ViewState state)
{
  const ViewState interactionMask = ViewState::FOCUSED +
                                    ViewState::PRESSED +
                                    ViewState::SELECTED;

  return state & interactionMask;
}

bool HasNoFocusedBit(ViewState state)
{
  return !static_cast<bool>(state & ViewState::FOCUSED);
}
```
