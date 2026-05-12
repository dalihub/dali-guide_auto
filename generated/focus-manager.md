---
title: Focus Manager
sidebar_label: Focus Manager
category: uncategorized
---

# Focus Manager

The Focus Manager provides keyboard focus navigation and focus chain management for dali-ui applications. It handles directional focus movement, focus groups, and focus change signals.

## Table of Contents

- [Setting and Requesting Focus](#setting-and-requesting-focus)
- [Moving Focus](#moving-focus)
- [Focus Groups](#focus-groups)
- [Focus Change Signals](#focus-change-signals)
- [Focus Indicator](#focus-indicator)
- [Window Focus Behavior](#window-focus-behavior)

## Setting and Requesting Focus

The Focus Manager provides two methods for setting focus: `RequestFocus()` and `SetCurrentFocusView()`. The key difference is that `RequestFocus()` performs child delegation for containers, while `SetCurrentFocusView()` sets focus directly.

### Requesting Focus with Child Delegation

Use `RequestFocus()` when you want the Focus Manager to automatically find a focusable descendant if the target is a container:

```cpp
using namespace Dali::Ui;

// Create a focusable view
View view = View::New().SetFocusable(true);
scene.Add(view);

// Request focus - the view receives focus
bool success = FocusManager::Get().RequestFocus(view);
```

When requesting focus on a `Layout` that is not itself focusable, the manager delegates to the first focusable descendant:

```cpp
auto layout = Layout::New();
View child1 = View::New();  // not focusable
View child2 = View::New().SetFocusable(true);
layout.Add(child1);
layout.Add(child2);
scene.Add(layout);

// Focus delegates to child2
FocusManager::Get().RequestFocus(layout);
// GetCurrentFocusView() returns child2
```

### Setting Focus Directly

Use `SetCurrentFocusView()` to set focus exactly to a specific view without delegation:

```cpp
View view = View::New().SetFocusable(true);
scene.Add(view);

// Set focus directly
bool success = FocusManager::Get().SetCurrentFocusView(view);
```

### Getting the Current Focus

Retrieve the currently focused view with `GetCurrentFocusView()`:

```cpp
View current = FocusManager::Get().GetCurrentFocusView();
if (current)
{
  // A view is currently focused
}
```

### Clearing Focus

Remove focus from all views:

```cpp
FocusManager::Get().ClearFocus();
```

## Moving Focus

The Focus Manager supports directional focus movement using `MoveFocus()` with `FocusDirection` values.

### Directional Movement

Move focus in cardinal directions:

```cpp
using namespace Dali::Ui;

// Move focus right
FocusManager::Get().MoveFocus(FocusDirection::RIGHT);

// Move focus left
FocusManager::Get().MoveFocus(FocusDirection::LEFT);

// Move focus up
FocusManager::Get().MoveFocus(FocusDirection::UP);

// Move focus down
FocusManager::Get().MoveFocus(FocusDirection::DOWN);
```

### Forward and Backward Navigation

Use `FORWARD` and `BACKWARD` for sequential navigation through the focus chain:

```cpp
// Move to next focusable view
FocusManager::Get().MoveFocus(FocusDirection::FORWARD);

// Move to previous focusable view
FocusManager::Get().MoveFocus(FocusDirection::BACKWARD);
```

The `MoveFocus()` method returns `true` if the movement was successful:

```cpp
if (!FocusManager::Get().MoveFocus(FocusDirection::FORWARD))
{
  // Already at the end of the focus chain
}
```

### Move Focus Backward History

Use `MoveFocusBackward()` to return to the previously focused view:

```cpp
FocusManager::Get().MoveFocusBackward();
```

### Focus Movement Priority

When moving focus, the Focus Manager resolves the target in this order:

1. **Parent chain navigation** - `FocusNavigationCallback` set on an ancestor
2. **Directional property** - explicit focusable view set via `SetRightFocusableView()` etc.
3. **FocusFinder** - geometry-based or linear ordering

## Focus Groups

A focus group traps keyboard focus within a view subtree. When a view is set as a focus group, focus cannot leave that subtree through directional navigation.

### Creating a Focus Group

```cpp
View group = View::New();
FocusManager::Get().SetAsFocusGroup(group, true);

View insideA = View::New().SetFocusable(true);
View insideB = View::New().SetFocusable(true);
group.Add(insideA);
group.Add(insideB);
scene.Add(group);

// Focus is trapped within the group
FocusManager::Get().RequestFocus(insideA);
FocusManager::Get().MoveFocus(FocusDirection::DOWN);  // Moves to insideB
FocusManager::Get().MoveFocus(FocusDirection::DOWN);  // Fails - cannot leave group
```

### Checking Focus Group Status

```cpp
bool isGroup = FocusManager::Get().IsFocusGroup(view);
```

### Getting the Containing Focus Group

Find the nearest ancestor that is a focus group:

```cpp
View focusGroup = FocusManager::Get().GetFocusGroup(view);
```

## Focus Change Signals

Connect to `FocusChangedSignal()` to react to focus changes:

```cpp
void OnFocusChanged(View previouslyFocused, View currentlyFocused)
{
  if (!currentlyFocused)
  {
    // Focus was cleared
  }
  else
  {
    // Focus moved to a new view
  }
}

// Connect the callback
FocusManager::Get().FocusChangedSignal().Connect(&OnFocusChanged);
```

### Getting Focus Change Device Information

Determine what caused the last focus change:

```cpp
FocusDevice device = FocusManager::Get().GetLastFocusChangeDevice();

switch (device)
{
  case FocusDevice::KEYBOARD:
    // Keyboard navigation
    break;
  case FocusDevice::TOUCH:
    // Touch interaction
    break;
  case FocusDevice::PROGRAMMATIC:
    // API call
    break;
  // ... other devices
}
```

Get the device name for more specific information:

```cpp
const Dali::String& deviceName = FocusManager::Get().GetLastFocusChangeDeviceName();
```

## Focus Indicator

The Focus Manager displays a focus indicator on the currently focused view. You can customize this indicator.

### Setting a Custom Focus Indicator

```cpp
View customIndicator = View::New();
// Configure the indicator appearance...

FocusManager::Get().SetFocusIndicatorActor(customIndicator);
```

### Getting the Current Focus Indicator

```cpp
View indicator = FocusManager::Get().GetFocusIndicatorView();
```

## Window Focus Behavior

By default, the Focus Manager clears focus when the application window loses focus. You can disable this behavior.

### Preserving Focus on Window Focus Lost

```cpp
// Disable auto-clear on window focus lost
FocusManager::Get().SetClearFocusOnWindowFocusLost(false);

// Check current setting
bool autoClear = FocusManager::Get().GetClearFocusOnWindowFocusLost();
```
