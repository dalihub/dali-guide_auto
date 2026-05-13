---
title: Focus Manager
sidebar_label: Focus Manager
category: uncategorized
---

# Focus Manager

The Focus Manager controls keyboard and directional focus navigation within a dali-ui application. It provides APIs to request, move, and clear focus, define focus groups, and respond to focus changes.

## Table of Contents

- [Getting the Focus Manager](#getting-the-focus-manager)
- [Requesting and Setting Focus](#requesting-and-setting-focus)
- [Moving Focus](#moving-focus)
- [Focus Groups](#focus-groups)
- [Focus Change Signals](#focus-change-signals)
- [Clearing Focus](#clearing-focus)
- [Focus Indicator](#focus-indicator)
- [Window Focus Behavior](#window-focus-behavior)

## Getting the Focus Manager

The `FocusManager` is a singleton. Use `FocusManager::Get()` to access the instance.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali::Ui;

FocusManager manager = FocusManager::Get();
```

## Requesting and Setting Focus

### Requesting Focus

`RequestFocus()` attempts to give focus to a view. The view must be focusable, enabled, and visible. When called on a `Layout`, it delegates to the first eligible descendant.

```cpp
View view = View::New().SetFocusable(true);
application.GetScene().Add(view);

bool success = FocusManager::Get().RequestFocus(view);
if (success) {
  // Focus was granted
  View current = FocusManager::Get().GetCurrentFocusView();
  // current == view
}
```

If the view is not focusable, `RequestFocus()` returns `false`:

```cpp
View view = View::New(); // focusable is false by default
application.GetScene().Add(view);

bool success = FocusManager::Get().RequestFocus(view);
// success == false
```

### Setting Focus Directly

`SetCurrentFocusView()` sets focus directly without delegation. Use this when you want explicit control over which view receives focus.

```cpp
View view = View::New().SetFocusable(true);
application.GetScene().Add(view);

bool success = FocusManager::Get().SetCurrentFocusView(view);
// success == true, focus is on view
```

Unlike `RequestFocus()`, `SetCurrentFocusView()` does not delegate to children when called on a `Layout`:

```cpp
auto layout = Layout::New(); // not focusable
View child = View::New().SetFocusable(true);
layout.Add(child);
application.GetScene().Add(layout);

bool success = FocusManager::Get().SetCurrentFocusView(layout);
// success == false (layout is not focusable, no delegation)
```

### Getting the Current Focus View

```cpp
View current = FocusManager::Get().GetCurrentFocusView();
if (current) {
  // A view has focus
}
```

## Moving Focus

### Directional Movement

Use `MoveFocus()` to move focus in a specific direction:

```cpp
View view1 = View::New().SetFocusable(true);
View view2 = View::New().SetFocusable(true);
view1.SetRightFocusableView(view2);
application.GetScene().Add(view1);
application.GetScene().Add(view2);

FocusManager::Get().RequestFocus(view1);

bool moved = FocusManager::Get().MoveFocus(FocusDirection::RIGHT);
// moved == true, focus is now on view2
```

Available directions include:
- `FocusDirection::LEFT`
- `FocusDirection::RIGHT`
- `FocusDirection::UP`
- `FocusDirection::DOWN`
- `FocusDirection::FORWARD`
- `FocusDirection::BACKWARD`
- `FocusDirection::CLOCKWISE`
- `FocusDirection::COUNTER_CLOCKWISE`

### Forward and Backward Navigation

`FORWARD` and `BACKWARD` navigate through focusable views in layout order:

```cpp
View parent = View::New();
View v1 = View::New().SetFocusable(true);
View v2 = View::New().SetFocusable(true);
View v3 = View::New().SetFocusable(true);
parent.Add(v1);
parent.Add(v2);
parent.Add(v3);
application.GetScene().Add(parent);

FocusManager::Get().RequestFocus(v1);

FocusManager::Get().MoveFocus(FocusDirection::FORWARD);
// focus on v2

FocusManager::Get().MoveFocus(FocusDirection::FORWARD);
// focus on v3

FocusManager::Get().MoveFocus(FocusDirection::BACKWARD);
// focus on v2
```

Non-focusable, invisible, and disabled views are skipped during navigation:

```cpp
View v1 = View::New().SetFocusable(true);
View v2 = View::New(); // not focusable
View v3 = View::New().SetFocusable(true).SetVisibility(false); // invisible
View v4 = View::New().SetFocusable(true).SetEnabled(false); // disabled
View v5 = View::New().SetFocusable(true);
// FORWARD from v1 skips v2, v3, v4 and lands on v5
```

### Move Focus Backward

`MoveFocusBackward()` moves focus to the previous focusable view:

```cpp
FocusManager::Get().MoveFocusBackward();
```

### Movement Resolution Priority

`MoveFocus()` resolves the next focus target in this order:

1. **Parent chain navigation callback** — If a parent has a `FocusNavigationCallback` set, it is consulted first.
2. **Directional property** — Explicit focusable view properties like `SetRightFocusableView()`.
3. **FocusFinder** — Geometry-based or linear ordering.

```cpp
// Example: directional property takes precedence over FocusFinder
View current = View::New().SetFocusable(true);
View viewProp = View::New().SetFocusable(true);
View viewFinder = View::New().SetFocusable(true);

current.SetRightFocusableView(viewProp);
// MoveFocus(RIGHT) goes to viewProp, not viewFinder
```

### Setting Directional Focus Targets

Use directional setters to explicitly define focus navigation paths:

```cpp
View center = View::New().SetFocusable(true);
View left = View::New().SetFocusable(true);
View right = View::New().SetFocusable(true);
View up = View::New().SetFocusable(true);
View down = View::New().SetFocusable(true);

center.SetLeftFocusableView(left)
      .SetRightFocusableView(right)
      .SetUpFocusableView(up)
      .SetDownFocusableView(down);
```

Additional directional setters:
- `SetForwardFocusableView()`
- `SetBackwardFocusableView()`
- `SetClockwiseFocusableView()`
- `SetCounterClockwiseFocusableView()`

## Focus Groups

A focus group limits `MoveFocus()` navigation to views within the group. This is useful for creating contained navigation areas.

### Creating a Focus Group

```cpp
View group = View::New();
FocusManager::Get().SetAsFocusGroup(group, true);

View insideA = View::New().SetFocusable(true);
View insideB = View::New().SetFocusable(true);
group.Add(insideA);
group.Add(insideB);

View outside = View::New().SetFocusable(true);
application.GetScene().Add(group);
application.GetScene().Add(outside);

FocusManager::Get().RequestFocus(insideA);
FocusManager::Get().MoveFocus(FocusDirection::DOWN);
// focus moves within group, not to outside
```

### Checking Focus Group Status

```cpp
bool isGroup = FocusManager::Get().IsFocusGroup(view);

View group = FocusManager::Get().GetFocusGroup(view);
```

### Focus Group Containment

When a view is inside a focus group, `MoveFocus()` is scoped to that group. Direct `RequestFocus()` calls can still target views outside the group.

```cpp
// MoveFocus is contained within the group
FocusManager::Get().MoveFocus(FocusDirection::FORWARD);

// RequestFocus bypasses containment
FocusManager::Get().RequestFocus(outside);
```

## Focus Change Signals

Connect to `FocusChangedSignal()` on a view to be notified when it gains or loses focus.

### Signal Handler Signature

```cpp
void OnFocusChanged(View view, bool focused);
```

### Connecting to the Signal

```cpp
struct FocusChangedSignalData
{
  bool called;
  bool focused;
  View view;
};

void OnFocusChanged(View view, bool focused)
{
  // focused == true: view gained focus
  // focused == false: view lost focus
}

// Connect the signal
view.FocusChangedSignal().Connect(&application, &OnFocusChanged);
```

### Example: Tracking Focus Changes

```cpp
View view1 = View::New().SetFocusable(true);
View view2 = View::New().SetFocusable(true);
application.GetScene().Add(view1);
application.GetScene().Add(view2);

FocusChangedSignalData data1;
FocusChangedSignalFunctor functor1(data1);
view1.FocusChangedSignal().Connect(&application, functor1);

FocusManager::Get().SetCurrentFocusView(view1);
// data1.called == true, data1.focused == true

FocusManager::Get().SetCurrentFocusView(view2);
// data1.called == true, data1.focused == false (view1 lost focus)
```

## Clearing Focus

`ClearFocus()` removes focus from the currently focused view:

```cpp
FocusManager::Get().ClearFocus();
```

When focus is cleared, the previously focused view receives a `FocusChangedSignal` with `focused == false`.

```cpp
view.FocusChangedSignal().Connect(&application, functor);
FocusManager::Get().SetCurrentFocusView(view);
FocusManager::Get().ClearFocus();
// functor called with focused == false
```

## Focus Indicator

The focus indicator is a visual element that highlights the currently focused view.

### Setting the Focus Indicator

```cpp
View indicator = View::New();
// Configure indicator appearance...
FocusManager::Get().SetFocusIndicatorActor(indicator);
```

### Getting the Focus Indicator

```cpp
View indicator = FocusManager::Get().GetFocusIndicatorView();
```

## Window Focus Behavior

### Clear on Window Focus Lost

Configure whether focus is automatically cleared when the window loses focus:

```cpp
// Enable automatic focus clearing
FocusManager::Get().SetClearFocusOnWindowFocusLost(true);

// Check current setting
bool enabled = FocusManager::Get().GetClearFocusOnWindowFocusLost();
```

### Last Focus Change Device

Query which input device caused the last focus change:

```cpp
FocusDevice device = FocusManager::Get().GetLastFocusChangeDevice();
const Dali::String& deviceName = FocusManager::Get().GetLastFocusChangeDeviceName();
```

## Blocking Descendant Focus

A view can block its descendants from receiving focus using `SetDescendantFocusBlocked()`:

```cpp
View parent = View::New();
parent.SetDescendantFocusBlocked(true);

View child = View::New().SetFocusable(true);
parent.Add(child);

bool success = FocusManager::Get().RequestFocus(child);
// success == false (child is blocked from focus)
```

Check if a view blocks descendant focus:

```cpp
bool blocked = view.IsDescendantFocusBlocked();
```

## Focus Navigation Callbacks

For custom navigation logic, set a `FocusNavigationCallback` on a parent view:

```cpp
View gCallbackTarget;

View MyNavigationCallback(View current, FocusDirection direction)
{
  return gCallbackTarget;
}

View parent = View::New();
View child1 = View::New().SetFocusable(true);
View child2 = View::New().SetFocusable(true);
parent.Add(child1);
parent.Add(child2);

gCallbackTarget = child2;
parent.SetFocusNavigationCallback(
  FocusNavigationCallback::New(&MyNavigationCallback));

FocusManager::Get().RequestFocus(child1);
FocusManager::Get().MoveFocus(FocusDirection::RIGHT);
// focus moves to child2 via callback
```

The callback takes priority over directional properties and FocusFinder during `MoveFocus()` resolution.
