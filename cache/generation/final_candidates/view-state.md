---
title: View State
sidebar_label: View State
category: views-components
---

# View State

`Dali::Ui::ViewState` is the dali-ui value type for testing, combining, naming, and comparing the visual or interaction state of a `Dali::Ui::View`.

## Table of Contents

- [Representing View State](#representing-view-state)
- [Combining and Testing States](#combining-and-testing-states)
- [Detecting State Transitions](#detecting-state-transitions)
- [Custom View States](#custom-view-states)
- [Debug-Friendly State Names](#debug-friendly-state-names)

## Representing View State

In dali-ui application code, treat `Dali::Ui::View` as the app-facing object and `Dali::Ui::ViewState` as the compact state value associated with that view. A default-constructed `Dali::Ui::ViewState` is equivalent to `Dali::Ui::ViewState::NORMAL`, which has no state bits set.

Common predefined states include `Dali::Ui::ViewState::FOCUSED`, `Dali::Ui::ViewState::PRESSED`, `Dali::Ui::ViewState::DISABLED`, `Dali::Ui::ViewState::PSEUDO_DISABLED`, and `Dali::Ui::ViewState::SELECTED`.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali-ui-foundation/public-api/view-state.h>

bool IsIdleViewState(const Dali::Ui::ViewState& state)
{
  return state.IsNormal() && state == Dali::Ui::ViewState::NORMAL;
}

bool HasInteractiveState(const Dali::Ui::ViewState& state)
{
  return state.Contains(Dali::Ui::ViewState::FOCUSED) ||
         state.Contains(Dali::Ui::ViewState::PRESSED) ||
         state.Contains(Dali::Ui::ViewState::SELECTED);
}

bool ShouldUseDisabledStyle(Dali::Ui::View view, const Dali::Ui::ViewState& state)
{
  (void)view;
  return state.IsAnyDisabled();
}
```

Use `Dali::Ui::ViewState::IsNormal()` when you need the semantic normal-state check, and use `Dali::Ui::ViewState::operator bool` when you only need to know whether any non-normal bit is present.

```cpp
bool HasAnyNonNormalState(const Dali::Ui::ViewState& state)
{
  return static_cast<bool>(state);
}

bool IsPlainNormalState()
{
  Dali::Ui::ViewState state;
  return !static_cast<bool>(state) && state.IsNormal();
}
```

## Combining and Testing States

`Dali::Ui::ViewState` is a bitmask-style value. Use `Dali::Ui::ViewState::operator+` or `Dali::Ui::ViewState::operator|` to combine states, `Dali::Ui::ViewState::operator-` to remove states, `Dali::Ui::ViewState::Contains()` to test full containment, and `Dali::Ui::ViewState::HasIntersectionWith()` to test overlap.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

Dali::Ui::ViewState MakeSelectedPressedState()
{
  return Dali::Ui::ViewState::SELECTED + Dali::Ui::ViewState::PRESSED;
}

bool IsSelectedAndPressed(const Dali::Ui::ViewState& state)
{
  return state.Contains(Dali::Ui::ViewState::SELECTED_PRESSED);
}

bool HasAttentionState(const Dali::Ui::ViewState& state)
{
  const Dali::Ui::ViewState attention =
    Dali::Ui::ViewState::FOCUSED | Dali::Ui::ViewState::PRESSED;

  return state.HasIntersectionWith(attention);
}

Dali::Ui::ViewState ClearTransientState(const Dali::Ui::ViewState& state)
{
  return state - Dali::Ui::ViewState::PRESSED;
}
```

Use predefined composite constants when they express the intent directly. `Dali::Ui::ViewState::SELECTED_PRESSED` contains both `Dali::Ui::ViewState::SELECTED` and `Dali::Ui::ViewState::PRESSED`; `Dali::Ui::ViewState::DISABLED_SELECTED` contains both `Dali::Ui::ViewState::DISABLED` and `Dali::Ui::ViewState::SELECTED`; `Dali::Ui::ViewState::SELECTED_FOCUSED` contains both `Dali::Ui::ViewState::SELECTED` and `Dali::Ui::ViewState::FOCUSED`.

```cpp
bool ShouldDrawSelectedFocusRing(const Dali::Ui::ViewState& state)
{
  return state.Contains(Dali::Ui::ViewState::SELECTED_FOCUSED);
}

bool CanShowPressedOverlay(const Dali::Ui::ViewState& state)
{
  return state.Contains(Dali::Ui::ViewState::PRESSED) &&
         !state.IsAnyDisabled();
}

Dali::Ui::ViewState GetKnownViewStateMask()
{
  return Dali::Ui::ViewState::ALL;
}
```

For masks, `Dali::Ui::ViewState::operator&`, `Dali::Ui::ViewState::operator^`, and `Dali::Ui::ViewState::operator~` are available when you need exact bitwise composition.

```cpp
Dali::Ui::ViewState ExtractSelectionState(const Dali::Ui::ViewState& state)
{
  return state & Dali::Ui::ViewState::SELECTED;
}

Dali::Ui::ViewState GetChangedBits(
  const Dali::Ui::ViewState& previous,
  const Dali::Ui::ViewState& current)
{
  return previous ^ current;
}

bool IsNotFocusedMaskInterestedInPressed()
{
  const Dali::Ui::ViewState mask = ~Dali::Ui::ViewState::FOCUSED;
  return mask.Contains(Dali::Ui::ViewState::PRESSED);
}
```

## Detecting State Transitions

When application logic receives previous and current state values for a `Dali::Ui::View`, use the transition helpers on the state you care about. `Dali::Ui::ViewState::WasAdded()` detects a newly added state, `Dali::Ui::ViewState::WasRemoved()` detects a removed state, and `Dali::Ui::ViewState::WasChanged()` detects either direction.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali-ui-foundation/public-api/view-state.h>

void UpdateFocusDecoration(
  Dali::Ui::View view,
  const Dali::Ui::ViewState& previous,
  const Dali::Ui::ViewState& current)
{
  (void)view;

  if(Dali::Ui::ViewState::FOCUSED.WasAdded(previous, current))
  {
    // Apply focused decoration for this Dali::Ui::View.
  }

  if(Dali::Ui::ViewState::FOCUSED.WasRemoved(previous, current))
  {
    // Remove focused decoration for this Dali::Ui::View.
  }
}

bool SelectionChanged(
  const Dali::Ui::ViewState& previous,
  const Dali::Ui::ViewState& current)
{
  return Dali::Ui::ViewState::SELECTED.WasChanged(previous, current);
}
```

For a composite watch mask, use `Dali::Ui::ViewState::AnyChanged()`. This is useful when the same code path should run if any of several state bits changed.

```cpp
bool NeedsInteractionStyleRefresh(
  const Dali::Ui::ViewState& previous,
  const Dali::Ui::ViewState& current)
{
  const Dali::Ui::ViewState interactionMask =
    Dali::Ui::ViewState::FOCUSED +
    Dali::Ui::ViewState::PRESSED +
    Dali::Ui::ViewState::SELECTED;

  return interactionMask.AnyChanged(previous, current);
}

bool DisabledStateChanged(
  const Dali::Ui::ViewState& previous,
  const Dali::Ui::ViewState& current)
{
  const Dali::Ui::ViewState disabledMask =
    Dali::Ui::ViewState::DISABLED +
    Dali::Ui::ViewState::PSEUDO_DISABLED;

  return disabledMask.AnyChanged(previous, current);
}
```

`Dali::Signal`, `Dali::ConnectionTracker`, and `Dali::Ui::StateEvent` may appear in dali-ui state-notification code paths. Keep signal connection code attached to the signal-owning class or trait, and keep `Dali::Ui::ViewState` transition checks focused on the previous and current state values passed by that notification path.

## Custom View States

Use `Dali::Ui::ViewState::Create()` when a component needs a named state beyond the predefined set. A custom state is still a `Dali::Ui::ViewState`, so it can be combined, compared, and queried with the same APIs.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

Dali::Ui::ViewState LoadingState()
{
  return Dali::Ui::ViewState::Create("Loading");
}

bool IsLoadingViewState(const Dali::Ui::ViewState& state)
{
  const Dali::Ui::ViewState loading = Dali::Ui::ViewState::Create("Loading");
  return state.Contains(loading);
}

Dali::Ui::ViewState MakeDisabledLoadingState()
{
  const Dali::Ui::ViewState loading = Dali::Ui::ViewState::Create("Loading");
  return Dali::Ui::ViewState::DISABLED + loading;
}
```

Calling `Dali::Ui::ViewState::Create()` with the same name returns the same state value. The names `"Normal"` and `"All"` map to `Dali::Ui::ViewState::NORMAL` and `Dali::Ui::ViewState::ALL`.

```cpp
bool CustomStateNamesAreStable()
{
  const Dali::Ui::ViewState first = Dali::Ui::ViewState::Create("Expanded");
  const Dali::Ui::ViewState second = Dali::Ui::ViewState::Create("Expanded");

  return first == second;
}

bool BuiltInNamesResolveToBuiltInStates()
{
  return Dali::Ui::ViewState::Create("Normal") == Dali::Ui::ViewState::NORMAL &&
         Dali::Ui::ViewState::Create("All") == Dali::Ui::ViewState::ALL;
}
```

## Debug-Friendly State Names

Use `Dali::Ui::ViewState::ToString()` for diagnostics, logging, and developer tooling. It returns a readable name for predefined, custom, normal, all, and composite state values.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

auto GetStateLabel(const Dali::Ui::ViewState& state)
{
  return state.ToString();
}

auto GetPressedSelectedLabel()
{
  const Dali::Ui::ViewState state =
    Dali::Ui::ViewState::SELECTED + Dali::Ui::ViewState::PRESSED;

  return state.ToString();
}

bool StateChangedExactly(
  const Dali::Ui::ViewState& previous,
  const Dali::Ui::ViewState& current)
{
  return previous != current;
}
```

`Dali::Ui::ViewState::ToString()` is best kept as a diagnostic helper. For application behavior, prefer direct checks such as `Dali::Ui::ViewState::Contains()`, `Dali::Ui::ViewState::WasAdded()`, `Dali::Ui::ViewState::WasRemoved()`, and `Dali::Ui::ViewState::AnyChanged()`.
