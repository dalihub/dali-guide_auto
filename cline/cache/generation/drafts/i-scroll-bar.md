---
title: I Scroll Bar
sidebar_label: I Scroll Bar
category: views-components
---

# I Scroll Bar

`IScrollBar` is an interface that defines the contract for scroll bar components responsible for displaying and controlling the scrolling of a target view in dali-ui applications.

## Table of Contents

- [Creating a Scroll Bar](#creating-a-scroll-bar)
- [Configuring Scroll Bar Visibility](#configuring-scroll-bar-visibility)
- [Updating Scroll Bar State](#updating-scroll-bar-state)
- [Hiding the Scroll Bar](#hiding-the-scroll-bar)

## Creating a Scroll Bar

The `ScrollBar` class implements the `IScrollBar` interface. Create a scroll bar using the static `ScrollBar::New()` method. Since `ScrollBar` inherits from `AbsoluteLayout`, you can add it to your view hierarchy like any other layout.

```cpp
#include <dali-ui-foundation/public-api/scroll-bar.h>

// Create a scroll bar
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

// Add to a parent view
parentView.Add(scrollBar);
```

You can also downcast a handle to a `ScrollBar` using `ScrollBar::DownCast()`:

```cpp
Dali::BaseHandle handle = scrollBar;
Dali::Ui::ScrollBar scrollBar2 = Dali::Ui::ScrollBar::DownCast(handle);

if (scrollBar2)
{
  // Successfully downcast
}
```

## Configuring Scroll Bar Visibility

The `IScrollBar` interface provides methods to control the visibility of vertical and horizontal scroll bars independently. Use the `ScrollBarVisibility` enum to set the desired behavior.

### Visibility Modes

The `ScrollBarVisibility` enum defines three visibility modes:

- `ScrollBarVisibility::Auto` - Shows the scroll bar only when scrolling is active
- `ScrollBarVisibility::Always` - Always shows the scroll bar
- `ScrollBarVisibility::Never` - Never shows the scroll bar

### Setting Visibility

```cpp
#include <dali-ui-foundation/public-api/scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

// Set vertical scroll bar to always visible
scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Always);

// Set horizontal scroll bar to auto (show only when scrolling)
scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
```

### Getting Current Visibility

```cpp
// Get current visibility settings
Dali::Ui::ScrollBarVisibility verticalVisibility = scrollBar.GetVerticalScrollBarVisibility();
Dali::Ui::ScrollBarVisibility horizontalVisibility = scrollBar.GetHorizontalScrollBarVisibility();
```

By default, both vertical and horizontal scroll bars use `ScrollBarVisibility::Auto`.

## Updating Scroll Bar State

The `IScrollBar` interface provides methods to update the scroll bar size and position based on the scrollable content and current scroll position.

### Updating Bar Size

Call `UpdateBarSize()` to recalculate the scroll bar thumb size based on the scrollable content dimensions and viewport size. The thumb size represents the proportion of visible content within the total scrollable area.

```cpp
// Define scrollable content and viewport dimensions
float scrollWidth = 1000.0f;    // Total scrollable content width
float scrollHeight = 2000.0f;   // Total scrollable content height
float viewportWidth = 400.0f;   // Visible viewport width
float viewportHeight = 600.0f;  // Visible viewport height

// Update scroll bar size to reflect the current content-to-viewport ratio
scrollBar.UpdateBarSize(scrollWidth, scrollHeight, viewportWidth, viewportHeight);
```

### Updating Scroll Position

Call `UpdateScrollPosition()` to move the scroll bar thumb to reflect the current scroll position of the content.

```cpp
// Update scroll bar position based on current scroll offset
Dali::Vector2 scrollPosition(150.0f, 300.0f);
scrollBar.UpdateScrollPosition(scrollPosition);
```

### Getting the Target View

Use `GetTarget()` to retrieve the view that this scroll bar is controlling:

```cpp
Dali::Ui::View targetView = scrollBar.GetTarget();
if (targetView)
{
  // The scroll bar has an associated target view
}
```

## Hiding the Scroll Bar

Call `HideBar()` to trigger a fade-out animation for the scroll bar. This is typically called when scrolling ends. The hide behavior only affects scroll bars with `ScrollBarVisibility::Auto` visibility mode.

```cpp
// Hide the scroll bar (fade out animation)
scrollBar.HideBar();
```

This method is useful when implementing custom scroll containers where you want to hide the scroll bar after the user stops scrolling.