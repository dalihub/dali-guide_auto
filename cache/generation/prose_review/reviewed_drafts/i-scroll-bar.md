---
title: IScrollBar
sidebar_label: IScrollBar
category: uncategorized
---

# IScrollBar

`IScrollBar` defines the interface for displaying and controlling scroll bars in dali-ui applications. It provides methods to configure visibility, update bar size, and synchronize scroll position with a target view.

## Table of Contents

- [Creating a Scroll Bar](#creating-a-scroll-bar)
- [Configuring Scroll Bar Visibility](#configuring-scroll-bar-visibility)
- [Updating Bar Size and Position](#updating-bar-size-and-position)
- [Hiding the Scroll Bar](#hiding-the-scroll-bar)

## Creating a Scroll Bar

The `IScrollBar` interface is implemented by the `ScrollBar` class. Use `ScrollBar::New()` to create a scroll bar instance.

```cpp
#include <dali-ui-foundation/public-api/scroll-bar.h>

using namespace Dali::Ui;

// Create a scroll bar
ScrollBar scrollBar = ScrollBar::New();
```

You can also downcast a handle to `ScrollBar` using `ScrollBar::DownCast()`:

```cpp
BaseHandle handle = scrollBar;
ScrollBar scrollBar2 = ScrollBar::DownCast(handle);
```

## Configuring Scroll Bar Visibility

`IScrollBar` provides independent control over vertical and horizontal scroll bar visibility using the `ScrollBarVisibility` enum:

- `ScrollBarVisibility::Auto` - Shows the scroll bar only during scrolling
- `ScrollBarVisibility::Always` - Always shows the scroll bar
- `ScrollBarVisibility::Never` - Never shows the scroll bar

### Setting Visibility

```cpp
// Always show the vertical scroll bar
scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Always);

// Never show the horizontal scroll bar
scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);
```

### Getting Current Visibility

```cpp
ScrollBarVisibility verticalVisibility = scrollBar.GetVerticalScrollBarVisibility();
ScrollBarVisibility horizontalVisibility = scrollBar.GetHorizontalScrollBarVisibility();
```

By default, both vertical and horizontal scroll bars use `ScrollBarVisibility::Auto`.

## Updating Bar Size and Position

### Updating Bar Size

Call `UpdateBarSize()` to recalculate the scroll bar thumb size based on the scrollable content and viewport dimensions:

```cpp
// scrollWidth, scrollHeight: total scrollable content size
// viewportWidth, viewportHeight: visible area size
scrollBar.UpdateBarSize(100.0f, 200.0f, 50.0f, 100.0f);
```

The scroll bar uses these dimensions to proportionally size the thumb indicator relative to the visible portion of the content.

### Updating Scroll Position

Synchronize the scroll bar position with the target view's scroll position using `UpdateScrollPosition()`:

```cpp
// Update the scroll bar to reflect a new scroll position
Vector2 scrollPosition(10.0f, 20.0f);
scrollBar.UpdateScrollPosition(scrollPosition);
```

### Getting the Target View

To retrieve the view associated with the scroll bar:

```cpp
View targetView = scrollBar.GetTarget();
```

## Hiding the Scroll Bar

Call `HideBar()` to trigger a fade-out animation for scroll bars with `ScrollBarVisibility::Auto`. This is typically invoked when scrolling ends:

```cpp
// Hide the scroll bar (fade out)
scrollBar.HideBar();
```

Note that `HideBar()` only affects scroll bars configured with `ScrollBarVisibility::Auto`. Scroll bars set to `ScrollBarVisibility::Always` remain visible.
