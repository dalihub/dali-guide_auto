---
title: Interactive View
sidebar_label: Interactive View
category: views-components
---

# Interactive View

InteractiveView is a View subclass with built-in interactive behavior for creating clickable UI components.

## Table of Contents

- [Creating an InteractiveView](#creating-an-interactiveview)
- [Configuring Click Behavior](#configuring-click-behavior)
- [Handling Click Events](#handling-click-events)
- [Responding to Press State Changes](#responding-to-press-state-changes)
- [Detecting Long Press Gestures](#detecting-long-press-gestures)
- [Disabling Interaction with Pseudo Disabled State](#disabling-interaction-with-pseudo-disabled-state)
- [Deriving Custom Interactive Components](#deriving-custom-interactive-components)

## Creating an InteractiveView

Create an `InteractiveView` using the static `New()` method. The view is clickable by default.

```cpp
using namespace Dali::Ui;

InteractiveView view = InteractiveView::New();
view.SetRequestedWidth(200.0f)
    .SetRequestedHeight(60.0f);
```

To downcast a base handle to an `InteractiveView`, use `DownCast()`:

```cpp
InteractiveView view = InteractiveView::DownCast(someHandle);
if (view)
{
  // Successfully downcast
}
```

## Configuring Click Behavior

### Enabling and Disabling Clicks

Control whether the view responds to clicks using `SetClickable()`. Query the current state with `IsClickable()`.

```cpp
InteractiveView view = InteractiveView::New();

view.SetClickable(false);  // Disable clicking
if (!view.IsClickable())
{
  // View will not respond to taps
}

view.SetClickable(true);   // Re-enable clicking
```

### Key Click Policy

For keyboard navigation, configure when a key press triggers a click using `SetKeyClickPolicy()`. Retrieve the current policy with `GetKeyClickPolicy()`.

```cpp
view.SetKeyClickPolicy(KeyClickPolicy::ON_PRESS);
KeyClickPolicy policy = view.GetKeyClickPolicy();
```

## Handling Click Events

Connect to the `ClickedSignal()` to respond when the user taps or clicks the view. The signal provides the view and the input event that triggered the click.

### Using ConnectClickedSignal for Fluent Chaining

The `ConnectClickedSignal()` method returns a reference to the view, enabling method chaining during setup:

```cpp
class MyController : public ConnectionTracker
{
public:
  void SetupView()
  {
    InteractiveView view = InteractiveView::New()
      .SetRequestedWidth(200.0f)
      .SetRequestedHeight(60.0f)
      .ConnectClickedSignal(this, &MyController::OnClicked);
  }

  void OnClicked(View view, InputEvent event)
  {
    // Handle click
  }
};
```

### Direct Signal Connection

Alternatively, connect to `ClickedSignal()` directly:

```cpp
view.ClickedSignal().Connect(this, &MyController::OnClicked);
```

Multiple handlers can be connected to the same signal:

```cpp
view.ConnectClickedSignal(this, &MyController::OnClickedFirst);
view.ConnectClickedSignal(this, &MyController::OnClickedSecond);
```

## Responding to Press State Changes

Track when the user presses down or releases by connecting to `PressedChangedSignal()`. The callback receives the view, a boolean indicating the pressed state, and the input event.

```cpp
class ButtonController : public ConnectionTracker
{
public:
  void CreateButton()
  {
    mButton = InteractiveView::New()
      .SetRequestedWidth(150.0f)
      .SetRequestedHeight(50.0f)
      .ConnectPressedChangedSignal(this, &ButtonController::OnPressedChanged);
  }

  void OnPressedChanged(View view, bool pressed, InputEvent event)
  {
    if (pressed)
    {
      // User pressed down - show visual feedback
    }
    else
    {
      // User released - restore normal appearance
    }
  }

private:
  InteractiveView mButton;
};
```

Query the current pressed state at any time using `IsPressed()`:

```cpp
if (view.IsPressed())
{
  // View is currently in pressed state
}
```

## Detecting Long Press Gestures

Connect to `LongPressedSignal()` to detect when the user performs a long press gesture. The handler must return a boolean: return `true` to suppress the subsequent click event, or `false` to allow both the long press and click to fire.

```cpp
class LongPressController : public ConnectionTracker
{
public:
  void SetupView()
  {
    InteractiveView view = InteractiveView::New()
      .SetRequestedWidth(100.0f)
      .SetRequestedHeight(100.0f)
      .ConnectLongPressedSignal(this, &LongPressController::OnLongPressed);
  }

  bool OnLongPressed(View view, InputEvent event)
  {
    // Handle long press
    return true;  // Suppress the click that would follow
  }
};
```

For direct signal connection:

```cpp
view.LongPressedSignal().Connect(this, &LongPressController::OnLongPressed);
```

## Disabling Interaction with Pseudo Disabled State

Use the pseudo disabled state to visually indicate that a view is non-interactive while keeping it in the view hierarchy. This is useful for showing disabled buttons or controls.

```cpp
InteractiveView view = InteractiveView::New();

view.SetPseudoDisabled(true);  // Mark as disabled
if (view.IsPseudoDisabled())
{
  // View is in pseudo disabled state
}

view.SetPseudoDisabled(false);  // Re-enable
```

Connect to `PseudoDisabledChangedSignal()` to react to state changes:

```cpp
view.PseudoDisabledChangedSignal().Connect(this, &MyController::OnPseudoDisabledChanged);

void OnPseudoDisabledChanged(View view, bool disabled)
{
  if (disabled)
  {
    // Update visual appearance to show disabled state
  }
}
```

## Deriving Custom Interactive Components

`InteractiveView` is designed as a base class for custom interactive components. Use the `DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS` macro in your derived class to inherit all chaining methods.

```cpp
class MyButton : public InteractiveView
{
public:
  MyButton() = default;

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
    // Set up button appearance
  }

  // Inherit all chaining methods from InteractiveView
  DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS(MyButton)

private:
  explicit MyButton(InteractiveView view)
  : InteractiveView(view)
  {
  }
};
```

The macro provides overridden `SetClickable()`, `SetPseudoDisabled()`, `SetKeyClickPolicy()`, and signal connection methods that return the derived type, preserving fluent chaining:

```cpp
MyButton button = MyButton::New("Click Me")
  .SetClickable(true)
  .SetPseudoDisabled(false)
  .ConnectClickedSignal(this, &MyController::OnButtonClicked);
```
