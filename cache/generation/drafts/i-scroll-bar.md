---
title: I Scroll Bar
sidebar_label: I Scroll Bar
category: uncategorized
---

# I Scroll Bar

`IScrollBar` defines the interface for scroll bar components that display and control the scrolling of a target view in dali-ui applications.

## Table of Contents

- [Overview](#overview)
- [Creating a Scroll Bar](#creating-a-scroll-bar)
- [Configuring Scroll Bar Visibility](#configuring-scroll-bar-visibility)
- [Updating Scroll Bar State](#updating-scroll-bar-state)

## Overview

`Dali::Ui::IScrollBar` is an abstract interface that defines the contract for scroll bar implementations. The concrete `Dali::Ui::ScrollBar` class implements this interface and provides additional styling properties. Scroll bars indicate the current scroll position and the relative size of the visible area within a scrollable view.

The interface supports independent control of vertical and horizontal scroll bar visibility through the `ScrollBarVisibility` enum:

- `ScrollBarVisibility::Auto` — Show the scroll bar only during scrolling
- `ScrollBarVisibility::Always` — Always show the scroll bar
- `ScrollBarVisibility::Never` — Never show the scroll bar

## Creating a Scroll Bar

Create a scroll bar using the `ScrollBar::New()` static method. Since `ScrollBar` implements `IScrollBar`, you can use the interface methods on the returned instance.

```cpp
#include <dali-ui-foundation/public-api/scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

using namespace Dali;
using namespace Dali::Ui;

// Create a scroll bar
ScrollBar scrollBar = ScrollBar::New();

// Use IScrollBar interface methods
scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto);
scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Auto);
```

## Configuring Scroll Bar Visibility

Control the visibility of vertical and horizontal scroll bars independently using `SetVerticalScrollBarVisibility()` and `SetHorizontalScrollBarVisibility()`.

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Always show the vertical scroll bar
scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Always);

// Hide the horizontal scroll bar
scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);

// Query current visibility settings
ScrollBarVisibility verticalVisibility = scrollBar.GetVerticalScrollBarVisibility();
ScrollBarVisibility horizontalVisibility = scrollBar.GetHorizontalScrollBarVisibility();
```

The default visibility for both scroll bars is `ScrollBarVisibility::Auto`, which shows the scroll bar only during active scrolling.

## Updating Scroll Bar State

### Updating Bar Size

Call `UpdateBarSize()` to recalculate the scroll bar thumb size based on the scrollable content area and viewport dimensions. The thumb size reflects the ratio of visible content to total content.

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Define scroll area and viewport dimensions
float scrollWidth = 1000.0f;   // Total scrollable width
float scrollHeight = 2000.0f;  // Total scrollable height
float viewportWidth = 400.0f;  // Visible width
float viewportHeight = 600.0f; // Visible height

// Update scroll bar size to reflect content proportions
scrollBar.UpdateBarSize(scrollWidth, scrollHeight, viewportWidth, viewportHeight);
```

### Updating Scroll Position

Call `UpdateScrollPosition()` to move the scroll bar thumb to reflect the current scroll position of the target view.

```cpp
// Update scroll bar position based on current scroll offset
Vector2 scrollPosition(150.0f, 300.0f);
scrollBar.UpdateScrollPosition(scrollPosition);
```

### Hiding the Scroll Bar

Call `HideBar()` to trigger a fade-out animation for scroll bars with `ScrollBarVisibility::Auto`. This method is typically called when scrolling ends.

```cpp
// Hide the scroll bar (fade out animation)
scrollBar.HideBar();
```

### Getting the Target View

Retrieve the target view that the scroll bar controls using `GetTarget()`.

```cpp
View targetView = scrollBar.GetTarget();
if (targetView)
{
    // Work with the target view
}
```
