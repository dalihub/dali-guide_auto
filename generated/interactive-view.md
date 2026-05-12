---
title: Interactive View
sidebar_label: Interactive View
category: uncategorized
---

# Interactive View

InteractiveView is a View subclass with built-in interactive behavior for handling clicks, long presses, and pressed state changes.

## Table of Contents

- [Creating an InteractiveView](#creating-an-interactiveview)
- [Configuring Click Behavior](#configuring-click-behavior)
- [Handling Click Events](#handling-click-events)
- [Responding to Pressed State Changes](#responding-to-pressed-state-changes)
- [Handling Long Press Gestures](#handling-long-press-gestures)
- [Disabling Interaction with Pseudo Disabled State](#disabling-interaction-with-pseudo-disabled-state)

## Creating an InteractiveView

Create an `InteractiveView` using the static `New()` method. The view is clickable by default.

```cpp
#include <dali-ui-foundation/public-api/interactive-view.h>

using namespace Dali::Ui;

// Create an interactive view
InteractiveView view = InteractiveView::New();

// Add to the view hierarchy
parent.Add(view);
```

Use `DownCast()` to safely convert a `BaseHandle` to an `InteractiveView`:

```cpp
BaseHandle handle = GetSomeHandle();
InteractiveView view = InteractiveView::DownCast(handle);
if (view)
{
    // Successfully downcast, use as InteractiveView
    view.SetClickable(true);
}
```

## Configuring Click Behavior

### Enabling and Disabling Clicks

By default, an `InteractiveView` is clickable. Use `SetClickable()` to enable or disable click handling:

```cpp
InteractiveView view = InteractiveView::New();

// Disable clicking
view.SetClickable(false);

// Check if clickable
if (view.IsClickable())
{
    // View accepts clicks
}

// Re-enable clicking
view.SetClickable(true);
```

### Key Click Policy

For keyboard, D-Pad, or remote control interactions, configure when the click event triggers using `SetKeyClickPolicy()`:

```cpp
InteractiveView view = InteractiveView::New();

// Trigger click on key press (immediate feedback)
view.SetKeyClickPolicy(KeyClickPolicy::ON_PRESS);

// Trigger click on key release (default behavior)
view.SetKeyClickPolicy(KeyClickPolicy::ON_RELEASE);

// Disable click events from key inputs entirely
view.SetKeyClickPolicy(KeyClickPolicy::DISABLED);

// Query current policy
KeyClickPolicy policy = view.GetKeyClickPolicy();
```

The `KeyClickPolicy` enum values are:

- `KeyClickPolicy::ON_RELEASE` — Trigger click when the key is released (default)
- `KeyClickPolicy::ON_PRESS` — Trigger click immediately when the key is pressed
- `KeyClickPolicy::DISABLED` — Disable click events from key inputs

## Handling Click Events

Connect to the `ClickedSignal()` to receive notifications when the view is clicked. The signal provides the view and the input event that triggered the click.

### Using Signal Connection

```cpp
void OnClicked(View view, InputEvent event)
{
    // Handle the click
}

// Connect to the signal
view.ClickedSignal().Connect(this, &OnClicked);
```

### Using ConnectClickedSignal for Chaining

Use `ConnectClickedSignal()` for fluent method chaining during setup:

```cpp
InteractiveView view = InteractiveView::New()
    .SetRequestedWidth(100.0f)
    .SetRequestedHeight(50.0f)
    .ConnectClickedSignal(this, [](View v, InputEvent e) {
        // Handle click inline
    });
```

Multiple handlers can be connected, and each call accumulates:

```cpp
view.ConnectClickedSignal(this, &MyClass::OnFirstHandler);
view.ConnectClickedSignal(this, &MyClass::OnSecondHandler);
// Both handlers will be called on click
```

## Responding to Pressed State Changes

Track the pressed state to provide visual feedback during touch interactions. The `PressedChangedSignal()` fires when the view enters or exits the pressed state.

```cpp
void OnPressedChanged(View view, bool pressed, InputEvent event)
{
    if (pressed)
    {
        // View is now pressed - apply pressed visual state
    }
    else
    {
        // View is no longer pressed - restore normal visual state
    }
}

view.PressedChangedSignal().Connect(this, &OnPressedChanged);
```

Use `ConnectPressedChangedSignal()` for method chaining:

```cpp
InteractiveView button = InteractiveView::New()
    .ConnectPressedChangedSignal(this, [](View v, bool pressed, InputEvent e) {
        // Update visual state based on pressed flag
    });
```

Query the current pressed state at any time:

```cpp
if (view.IsPressed())
{
    // The view is currently in pressed state
}
```

## Handling Long Press Gestures

Connect to `LongPressedSignal()` to detect long press gestures. The handler must return a `bool`:

- Return `true` to suppress the subsequent click event
- Return `false` to allow the click event to fire after the long press

```cpp
bool OnLongPressed(View view, InputEvent event)
{
    // Handle long press
    // Return true to consume the event and prevent click
    return true;
}

view.LongPressedSignal().Connect(this, &OnLongPressed);
```

Use `ConnectLongPressedSignal()` for fluent setup:

```cpp
InteractiveView view = InteractiveView::New()
    .ConnectLongPressedSignal(this, [](View v, InputEvent e) -> bool {
        // Show context menu or perform action
        return true; // Suppress click
    });
```

## Disabling Interaction with Pseudo Disabled State

Use the pseudo disabled state to visually indicate a disabled view while keeping it in the view hierarchy. This differs from `SetClickable(false)` by providing a distinct visual state.

```cpp
InteractiveView view = InteractiveView::New();

// Set pseudo disabled state
view.SetPseudoDisabled(true);

// Check current state
if (view.IsPseudoDisabled())
{
    // View is in pseudo disabled state
}

// Re-enable the view
view.SetPseudoDisabled(false);
```

Connect to `PseudoDisabledChangedSignal()` to react to state changes:

```cpp
void OnPseudoDisabledChanged(View view, bool disabled)
{
    if (disabled)
    {
        // Update appearance for disabled state
    }
    else
    {
        // Restore normal appearance
    }
}

view.PseudoDisabledChangedSignal().Connect(this, &OnPseudoDisabledChanged);
