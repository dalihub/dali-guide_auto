---
title: Interactive View
sidebar_label: Interactive View
category: views-components
---

# Interactive View

`InteractiveView` is a `View` subclass with built-in interactive behavior for creating clickable UI components.

## Table of Contents

- [Creating an Interactive View](#creating-an-interactive-view)
- [Handling Click Events](#handling-click-events)
- [Tracking Press State](#tracking-press-state)
- [Long Press Gestures](#long-press-gestures)
- [Disabling Interaction](#disabling-interaction)
- [Key Input Behavior](#key-input-behavior)
- [Creating Custom Interactive Controls](#creating-custom-interactive-controls)

## Creating an Interactive View

Create an `InteractiveView` using the static `New()` method. The view is clickable by default when added to a scene.

```cpp
#include <dali-ui-foundation/public-api/interactive-view.h>

using namespace Dali::Ui;

InteractiveView view = InteractiveView::New();
view.SetRequestedWidth(200.0f)
    .SetRequestedHeight(60.0f);

application.GetScene().Add(view);
```

Use `DownCast()` to safely convert a `BaseHandle` to an `InteractiveView`:

```cpp
BaseHandle handle = /* ... */;
InteractiveView view = InteractiveView::DownCast(handle);
if (view)
{
    // Successfully downcast
}
```

## Handling Click Events

Connect to `ClickedSignal()` to respond to tap or click interactions. The signal provides the source `View` and the `InputEvent` that triggered the click.

### Using Signal Connection

```cpp
class MyController : public ConnectionTracker
{
public:
    void SetupButton()
    {
        InteractiveView button = InteractiveView::New();
        button.ClickedSignal().Connect(this, &MyController::OnButtonClicked);
        application.GetScene().Add(button);
    }

    void OnButtonClicked(View view, InputEvent event)
    {
        // Handle click
    }
};
```

### Using Fluent Chaining

The `ConnectClickedSignal()` method returns a reference to the view, enabling fluent setup:

```cpp
InteractiveView button = InteractiveView::New()
    .SetRequestedWidth(220.0f)
    .SetRequestedHeight(80.0f)
    .ConnectClickedSignal(this, &MyController::OnButtonClicked);

application.GetScene().Add(button);
```

Multiple handlers can be connected to the same view:

```cpp
button.ConnectClickedSignal(this, &MyController::OnFirstHandler);
button.ConnectClickedSignal(this, &MyController::OnSecondHandler);
```

## Tracking Press State

`InteractiveView` tracks the pressed state automatically. Use `PressedChangedSignal()` to react to press and release events.

### Checking Press State

```cpp
InteractiveView view = InteractiveView::New();

if (view.IsPressed())
{
    // View is currently pressed
}
```

### Responding to State Changes

The `PressedChangedSignal` callback receives the view, a boolean indicating the new pressed state, and the input event:

```cpp
class MyController : public ConnectionTracker
{
public:
    void SetupView()
    {
        InteractiveView view = InteractiveView::New();
        view.PressedChangedSignal().Connect(this, &MyController::OnPressedChanged);
    }

    void OnPressedChanged(View view, bool pressed, InputEvent event)
    {
        if (pressed)
        {
            // Visual feedback for press
        }
        else
        {
            // Visual feedback for release
        }
    }
};
```

Use `ConnectPressedChangedSignal()` for fluent setup:

```cpp
InteractiveView view = InteractiveView::New()
    .SetRequestedWidth(100.0f)
    .SetRequestedHeight(100.0f)
    .ConnectPressedChangedSignal(this, &MyController::OnPressedChanged);
```

## Long Press Gestures

Connect to `LongPressedSignal()` to handle long press gestures. The handler must return a boolean: return `true` to suppress the subsequent click event, or `false` to allow it.

```cpp
class MyController : public ConnectionTracker
{
public:
    void SetupView()
    {
        InteractiveView view = InteractiveView::New();
        view.LongPressedSignal().Connect(this, &MyController::OnLongPressed);
    }

    bool OnLongPressed(View view, InputEvent event)
    {
        // Handle long press
        return true; // Suppress the click that would follow
    }
};
```

Use `ConnectLongPressedSignal()` for fluent chaining:

```cpp
InteractiveView view = InteractiveView::New()
    .SetRequestedWidth(100.0f)
    .SetRequestedHeight(100.0f)
    .ConnectLongPressedSignal(this, &MyController::OnLongPressed);
```

## Disabling Interaction

### Controlling Clickability

Use `SetClickable()` to enable or disable click handling:

```cpp
InteractiveView view = InteractiveView::New();

view.SetClickable(false);  // Disable clicking
if (!view.IsClickable())
{
    // View will not respond to clicks
}

view.SetClickable(true);   // Re-enable clicking
```

### Pseudo Disabled State

The pseudo disabled state provides a way to mark a view as disabled without removing it from the scene. Connect to `PseudoDisabledChangedSignal()` to react to state changes:

```cpp
InteractiveView view = InteractiveView::New();

view.SetPseudoDisabled(true);
if (view.IsPseudoDisabled())
{
    // View is in pseudo disabled state
}

// Monitor state changes
view.PseudoDisabledChangedSignal().Connect(this, &MyController::OnPseudoDisabledChanged);
```

## Key Input Behavior

Control how key events trigger clicks using `KeyClickPolicy`. The policy determines whether key presses trigger the click on key down, key up, or other behaviors.

```cpp
InteractiveView view = InteractiveView::New();

view.SetKeyClickPolicy(KeyClickPolicy::ON_PRESS);

KeyClickPolicy policy = view.GetKeyClickPolicy();
```

## Creating Custom Interactive Controls

`InteractiveView` is designed as a base class for custom interactive controls. Use the `DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS` macro to forward setter methods to the base class while returning the derived type.

```cpp
#include <dali-ui-foundation/public-api/interactive-view.h>

using namespace Dali;
using namespace Dali::Ui;

class MyButton : public InteractiveView
{
public:
    static MyButton New(const Dali::String& text)
    {
        MyButton button(InteractiveView::New());
        button.Initialize(text);
        return button;
    }

    static MyButton DownCast(BaseHandle handle)
    {
        InteractiveView view = InteractiveView::DownCast(handle);
        return view ? MyButton(view) : MyButton();
    }

    void Initialize(const Dali::String& text)
    {
        // Set up visual appearance
    }

    // Forwards SetClickable, SetPseudoDisabled, SetKeyClickPolicy,
    // ConnectClickedSignal, ConnectPressedChangedSignal, ConnectLongPressedSignal
    // with the derived return type
    DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS(MyButton)

private:
    explicit MyButton(InteractiveView view)
    : InteractiveView(view)
    {
    }
};
```

The macro enables fluent chaining with the derived type:

```cpp
MyButton button = MyButton::New("Click Me")
    .SetClickable(true)
    .SetPseudoDisabled(false)
    .ConnectClickedSignal(this, &MyController::OnButtonClicked);