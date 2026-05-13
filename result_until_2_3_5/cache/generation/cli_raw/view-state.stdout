---
title: View State
sidebar_label: View State
category: styling-theme-config
---

# View State

`Dali::Ui::ViewState` represents the visual and interaction state of a `Dali::Ui::View`, such as focused, pressed, disabled, selected, or a combination of those states.

## Table of Contents

- [Representing View State](#representing-view-state)
- [Combining and Matching States](#combining-and-matching-states)
- [Detecting State Transitions](#detecting-state-transitions)
- [Creating Custom States](#creating-custom-states)
- [Debugging and Broad State Masks](#debugging-and-broad-state-masks)

## Representing View State

A `Dali::Ui::ViewState` is a small value object. The default constructed value is `Dali::Ui::ViewState::NORMAL`, which means no state bits are set.

Use predefined states such as `Dali::Ui::ViewState::FOCUSED`, `Dali::Ui::ViewState::PRESSED`, `Dali::Ui::ViewState::DISABLED`, `Dali::Ui::ViewState::PSEUDO_DISABLED`, and `Dali::Ui::ViewState::SELECTED` when writing state-dependent styling or interaction logic for a `Dali::Ui::View`.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

void InspectBasicState()
{
  Dali::Ui::ViewState state;

  if(state.IsNormal())
  {
    // The state is Dali::Ui::ViewState::NORMAL.
  }

  if(state == Dali::Ui::ViewState::NORMAL)
  {
    // Equivalent check using Dali::Ui::ViewState::operator==.
  }

  if(!static_cast<bool>(state))
  {
    // Dali::Ui::ViewState::operator bool is false for NORMAL.
  }
}
```

`Dali::Ui::ViewState::IsAnyDisabled` is useful when a style should treat `Dali::Ui::ViewState::DISABLED` and `Dali::Ui::ViewState::PSEUDO_DISABLED` similarly.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

bool ShouldUseDisabledStyle(const Dali::Ui::ViewState& state)
{
  return state.IsAnyDisabled();
}
```

## Combining and Matching States

A `Dali::Ui::ViewState` can contain more than one state bit. Use `Dali::Ui::ViewState::operator+` or `Dali::Ui::ViewState::operator|` to combine states, and use `Dali::Ui::ViewState::Contains` to test whether all bits from another state are present.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

bool UsePressedSelectedStyle(const Dali::Ui::ViewState& current)
{
  const Dali::Ui::ViewState selectedPressed =
    Dali::Ui::ViewState::SELECTED + Dali::Ui::ViewState::PRESSED;

  return current.Contains(selectedPressed);
}
```

DALi also provides predefined composite states for common style selectors: `Dali::Ui::ViewState::SELECTED_PRESSED`, `Dali::Ui::ViewState::DISABLED_SELECTED`, and `Dali::Ui::ViewState::SELECTED_FOCUSED`.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

bool ShouldDrawFocusedSelection(const Dali::Ui::ViewState& current)
{
  return current.Contains(Dali::Ui::ViewState::SELECTED_FOCUSED);
}
```

Use `Dali::Ui::ViewState::HasIntersectionWith` when any overlap is enough. This is different from `Dali::Ui::ViewState::Contains`, which requires the full queried state to be present.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

bool HasInteractiveHighlight(const Dali::Ui::ViewState& current)
{
  const Dali::Ui::ViewState highlightStates =
    Dali::Ui::ViewState::FOCUSED + Dali::Ui::ViewState::PRESSED;

  return current.HasIntersectionWith(highlightStates);
}
```

Use `Dali::Ui::ViewState::operator-` to remove state bits from a combined value.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

Dali::Ui::ViewState ClearPressedState(const Dali::Ui::ViewState& current)
{
  return current - Dali::Ui::ViewState::PRESSED;
}
```

## Detecting State Transitions

State transitions are usually handled from `Dali::Ui::View` state change callbacks. The transition itself can be reasoned about with previous and current `Dali::Ui::ViewState` values.

Use `Dali::Ui::ViewState::WasAdded`, `Dali::Ui::ViewState::WasRemoved`, and `Dali::Ui::ViewState::WasChanged` when watching one specific state.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

void ApplyFocusTransition(const Dali::Ui::ViewState& previous,
                          const Dali::Ui::ViewState& current)
{
  if(Dali::Ui::ViewState::FOCUSED.WasAdded(previous, current))
  {
    // The View gained focus.
  }

  if(Dali::Ui::ViewState::FOCUSED.WasRemoved(previous, current))
  {
    // The View lost focus.
  }

  if(Dali::Ui::ViewState::PRESSED.WasChanged(previous, current))
  {
    // The pressed state toggled in either direction.
  }
}
```

Use `Dali::Ui::ViewState::AnyChanged` for a mask that contains multiple states. This is practical for style invalidation because a single check can cover several visual states.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

bool NeedsHighlightRestyle(const Dali::Ui::ViewState& previous,
                           const Dali::Ui::ViewState& current)
{
  const Dali::Ui::ViewState watchedStates =
    Dali::Ui::ViewState::FOCUSED +
    Dali::Ui::ViewState::PRESSED +
    Dali::Ui::ViewState::SELECTED;

  return watchedStates.AnyChanged(previous, current);
}
```

When connecting state-change logic through public signals, keep the signal ownership on the owning object or trait. `Dali::ConnectionTracker` can be used as the connection lifetime owner, and `Dali::Signal` is the signal mechanism used by the public API surface.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/view-state.h>

class StateStyleController : public Dali::ConnectionTracker
{
public:
  bool ShouldUpdateStyle(const Dali::Ui::ViewState& previous,
                         const Dali::Ui::ViewState& current) const
  {
    const Dali::Ui::ViewState visualStates =
      Dali::Ui::ViewState::FOCUSED +
      Dali::Ui::ViewState::PRESSED +
      Dali::Ui::ViewState::DISABLED +
      Dali::Ui::ViewState::SELECTED;

    return visualStates.AnyChanged(previous, current);
  }
};
```

## Creating Custom States

Use `Dali::Ui::ViewState::Create` when your application or styling layer needs an app-specific state name. A repeated call with the same name returns the same state value.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

Dali::Ui::ViewState LoadingState()
{
  return Dali::Ui::ViewState::Create("Loading");
}

bool ShouldShowLoadingStyle(const Dali::Ui::ViewState& current)
{
  const Dali::Ui::ViewState loading = Dali::Ui::ViewState::Create("Loading");
  return current.Contains(loading);
}
```

Custom states combine with predefined states in the same way as built-in states.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

Dali::Ui::ViewState LoadingAndFocusedState()
{
  const Dali::Ui::ViewState loading = Dali::Ui::ViewState::Create("Loading");

  return loading + Dali::Ui::ViewState::FOCUSED;
}
```

`Dali::Ui::ViewState::Create("Normal")` returns `Dali::Ui::ViewState::NORMAL`, and `Dali::Ui::ViewState::Create("All")` returns `Dali::Ui::ViewState::ALL`.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

bool IsReservedStateName()
{
  const Dali::Ui::ViewState normal = Dali::Ui::ViewState::Create("Normal");
  const Dali::Ui::ViewState all    = Dali::Ui::ViewState::Create("All");

  return normal == Dali::Ui::ViewState::NORMAL &&
         all == Dali::Ui::ViewState::ALL;
}
```

## Debugging and Broad State Masks

Use `Dali::Ui::ViewState::ToString` when logging or debugging state transitions. It returns names such as `Normal`, `All`, or a comma-separated set of registered state names.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

auto MakeStateDebugText(const Dali::Ui::ViewState& state)
{
  return state.ToString();
}
```

Use `Dali::Ui::ViewState::ALL` as a broad mask when every registered state should be considered. `Dali::Ui::ViewState::operator&`, `Dali::Ui::ViewState::operator^`, and `Dali::Ui::ViewState::operator~` are available for mask-style operations.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

Dali::Ui::ViewState ChangedBits(const Dali::Ui::ViewState& previous,
                                const Dali::Ui::ViewState& current)
{
  return (previous ^ current) & Dali::Ui::ViewState::ALL;
}

bool ChangedFromNormal(const Dali::Ui::ViewState& previous,
                       const Dali::Ui::ViewState& current)
{
  const Dali::Ui::ViewState changed = ChangedBits(previous, current);
  return static_cast<bool>(changed);
}
```

`Dali::Ui::ViewState::operator~` creates the complement mask for the current value. For example, it can describe every state except one specific state.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

bool HasAnyStateExceptFocused(const Dali::Ui::ViewState& current)
{
  const Dali::Ui::ViewState notFocused = ~Dali::Ui::ViewState::FOCUSED;
  return current.HasIntersectionWith(notFocused);
}
```
