---
title: Focus Manager
sidebar_label: Focus Manager
category: input-interaction
---

# Focus Manager

The Focus Manager provides keyboard focus navigation and maintains the focus chain for dali-ui applications. It handles focus movement in multiple directions, manages focus groups, and emits signals when focus changes.

## Table of Contents

- [Getting the Focus Manager](#getting-the-focus-manager)
- [Setting Focus](#setting-focus)
- [Moving Focus](#moving-focus)
- [Focus Groups](#focus-groups)
- [Focus Indicator](#focus-indicator)
- [Focus Change Signals](#focus-change-signals)
- [Window Focus Behavior](#window-focus-behavior)
- [Focus Device Information](#focus-device-information)

## Getting the Focus Manager

The `FocusManager` is a singleton. Access it using `FocusManager::Get()`:

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali::Ui;

// Get the singleton instance
FocusManager focusManager = FocusManager::Get();
```

## Setting Focus

### Requesting Focus

Use `RequestFocus()` to request focus on a view. This method supports child delegation: if the target is a container with focusable descendants, focus is delegated to the first eligible child.

```cpp
View view = View::New().SetFocusable(true);
scene.Add(view);

// Request focus - returns true if successful
bool success = FocusManager::Get().RequestFocus(view);
```

The view must be focusable and on the scene. A view becomes focusable by calling `SetFocusable(true)`:

```cpp
View button = View::New().SetFocusable(true);
```

### Setting Focus Directly

Use `SetCurrentFocusView()` to set focus directly without child delegation:

```cpp
View view = View::New().SetFocusable(true);
scene.Add(view);

// Set focus directly to the specified view
bool success = FocusManager::Get().SetCurrentFocusView(view);
```

### Getting the Current Focus

Retrieve the currently focused view with `GetCurrentFocusView()`:

```cpp
View currentFocus = FocusManager::Get().GetCurrentFocusView();
if (currentFocus)
{
  // A view is currently focused
}
```

### Clearing Focus

Remove focus from all views using `ClearFocus()`:

```cpp
FocusManager::Get().ClearFocus();
```

## Moving Focus

### Directional Focus Movement

Move focus programmatically using `MoveFocus()` with a `FocusDirection`:

```cpp
// Move focus right
bool moved = FocusManager::Get().MoveFocus(FocusDirection::RIGHT);

// Move focus left
FocusManager::Get().MoveFocus(FocusDirection::LEFT);

// Move focus up
FocusManager::Get().MoveFocus(FocusDirection::UP);

// Move focus down
FocusManager::Get().MoveFocus(FocusDirection::DOWN);
```

Available `FocusDirection` values:

- `FocusDirection::LEFT` - Move focus left
- `FocusDirection::RIGHT` - Move focus right
- `FocusDirection::UP` - Move focus up
- `FocusDirection::DOWN` - Move focus down
- `FocusDirection::FORWARD` - Move focus forward in traversal order
- `FocusDirection::BACKWARD` - Move focus backward in traversal order
- `FocusDirection::PAGE_UP` - Move to previous page
- `FocusDirection::PAGE_DOWN` - Move to next page
- `FocusDirection::CLOCKWISE` - Move clockwise
- `FocusDirection::COUNTER_CLOCKWISE` - Move counter-clockwise

### Forward and Backward Navigation

Use `FORWARD` and `BACKWARD` directions for sequential navigation:

```cpp
// Move to next focusable view
FocusManager::Get().MoveFocus(FocusDirection::FORWARD);

// Move to previous focusable view
FocusManager::Get().MoveFocus(FocusDirection::BACKWARD);
```

### Move Focus Backward

Use `MoveFocusBackward()` to return to the previously focused view:

```cpp
FocusManager::Get().MoveFocusBackward();
```

## Focus Groups

A focus group traps focus within a view's subtree. When a view is set as a focus group, keyboard navigation cannot leave that boundary.

### Creating a Focus Group

```cpp
View groupContainer = View::New();
FocusManager::Get().SetAsFocusGroup(groupContainer, true);
```

### Checking Focus Group Status

```cpp
bool isGroup = FocusManager::Get().IsFocusGroup(view);
```

### Getting the Containing Focus Group

Find the focus group that contains a view:

```cpp
View focusGroup = FocusManager::Get().GetFocusGroup(view);
```

## Focus Indicator

The focus manager displays a visual indicator on the focused view. You can customize this indicator.

### Setting a Custom Focus Indicator

```cpp
View customIndicator = View::New();
// Configure the indicator appearance...
FocusManager::Get().SetFocusIndicatorActor(customIndicator);
```

### Getting the Focus Indicator

```cpp
View indicator = FocusManager::Get().GetFocusIndicatorView();
```

## Focus Change Signals

Connect to `FocusChangedSignal()` to react to focus changes:

```cpp
void OnFocusChanged(View previouslyFocused, View currentlyFocused)
{
  if (previouslyFocused)
  {
    // Handle focus loss
  }
  if (currentlyFocused)
  {
    // Handle focus gain
  }
}

// Connect the callback
FocusManager::Get().FocusChangedSignal().Connect(&OnFocusChanged);
```

The signal provides both the previously focused view and the newly focused view.

## Window Focus Behavior

Control whether focus is cleared when the window loses focus:

### Setting Clear on Window Focus Lost

```cpp
// Enable clearing focus when window loses focus (default)
FocusManager::Get().SetClearFocusOnWindowFocusLost(true);

// Disable to preserve focus state
FocusManager::Get().SetClearFocusOnWindowFocusLost(false);
```

### Getting the Current Setting

```cpp
bool clearsOnLost = FocusManager::Get().GetClearFocusOnWindowFocusLost();
```

## Focus Device Information

Determine what input device caused the last focus change:

### Getting the Focus Device Type

```cpp
FocusDevice device = FocusManager::Get().GetLastFocusChangeDevice();

switch (device)
{
  case FocusDevice::KEYBOARD:
    // Keyboard initiated the focus change
    break;
  case FocusDevice::MOUSE:
    // Mouse click initiated the focus change
    break;
  case FocusDevice::TOUCH:
    // Touch initiated the focus change
    break;
  case FocusDevice::PROGRAMMATIC:
    // API call initiated the focus change
    break;
  // Other device types...
}
```

Available `FocusDevice` values:

- `FocusDevice::UNKNOWN` - Unknown device
- `FocusDevice::KEYBOARD` - Keyboard or attached buttons
- `FocusDevice::MOUSE` - Mouse, trackball, or touchpad
- `FocusDevice::TOUCH` - Touchscreen or stylus
- `FocusDevice::PEN` - Pen device
- `FocusDevice::POINTER` - Laser or infrared pointer
- `FocusDevice::GAMEPAD` - Gamepad or joystick
- `FocusDevice::WHEEL` - Mouse wheel
- `FocusDevice::PROGRAMMATIC` - API call, not a device

### Getting the Device Name

```cpp
const Dali::String& deviceName = FocusManager::Get().GetLastFocusChangeDeviceName();
```
