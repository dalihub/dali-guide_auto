---
title: Scroll Bar
sidebar_label: Scroll Bar
category: views-components
---

# Scroll Bar

The Scroll Bar component provides visual feedback for scrollable content position and allows users to navigate through scrollable views.

## Table of Contents

- [Creating a Scroll Bar](#creating-a-scroll-bar)
- [Configuring Bar Appearance](#configuring-bar-appearance)
- [Controlling Visibility](#controlling-visibility)
- [Managing Scroll Position](#managing-scroll-position)
- [Using with ScrollView](#using-with-scrollview)

## Creating a Scroll Bar

Create a `ScrollBar` instance using the static `New()` method:

```cpp
#include <dali-ui-foundation/public-api/scroll-bar.h>

using namespace Dali::Ui;

ScrollBar scrollBar = ScrollBar::New();
```

The `ScrollBar` class inherits from `AbsoluteLayout`, so you can position and size it like any other view. To downcast a base handle to a `ScrollBar`, use `ScrollBar::DownCast()`:

```cpp
BaseHandle handle = scrollBar;
ScrollBar casted = ScrollBar::DownCast(handle);
if (casted)
{
  // Successfully downcast
}
```

## Configuring Bar Appearance

### Thickness and Color

Set the scroll bar thickness using `SetBarThickness()`:

```cpp
scrollBar.SetBarThickness(10.0f);
float thickness = scrollBar.GetBarThickness(); // Returns 10.0f
```

Set the scroll bar color using `SetBarColor()`:

```cpp
scrollBar.SetBarColor(Vector4(0.2f, 0.4f, 0.8f, 1.0f)); // RGBA
Vector4 color = scrollBar.GetBarColor();
```

### Corner Radius

Apply rounded corners to the scroll bar. You can set the same radius for all corners or specify individual values:

```cpp
// Same radius for all corners
scrollBar.SetBarCornerRadius(4.0f);

// Individual radius for each corner (topLeft, topRight, bottomRight, bottomLeft)
scrollBar.SetBarCornerRadius(Vector4(2.0f, 4.0f, 4.0f, 2.0f));

Vector4 radius = scrollBar.GetBarCornerRadius();
```

### Minimum Size and Offset

Set a minimum size to ensure the scroll bar remains visible even when content is large:

```cpp
scrollBar.SetBarMinSize(20.0f);
float minSize = scrollBar.GetBarMinSize();
```

Set the offset to create a gap between the bar and the container edges:

```cpp
scrollBar.SetBarOffset(5.0f);
float offset = scrollBar.GetBarOffset();
```

## Controlling Visibility

The `ScrollBarVisibility` enum controls when scroll bars appear:

- `ScrollBarVisibility::Auto` — Show only during scrolling
- `ScrollBarVisibility::Always` — Always visible
- `ScrollBarVisibility::Never` — Never visible

Control vertical and horizontal scroll bar visibility independently:

```cpp
scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto);
scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);

ScrollBarVisibility vVisibility = scrollBar.GetVerticalScrollBarVisibility();
ScrollBarVisibility hVisibility = scrollBar.GetHorizontalScrollBarVisibility();
```

## Managing Scroll Position

### Setting and Getting Position

Set the scroll position programmatically:

```cpp
scrollBar.SetScrollPosition(Vector2(15.0f, 25.0f));
Vector2 position = scrollBar.GetScrollPosition();
```

### Updating Bar Size

When using `ScrollBar` standalone, call `UpdateBarSize()` to recalculate the bar proportions based on content and viewport dimensions:

```cpp
float scrollWidth = 100.0f;   // Total scrollable content width
float scrollHeight = 200.0f;  // Total scrollable content height
float viewportWidth = 50.0f;  // Visible viewport width
float viewportHeight = 100.0f; // Visible viewport height

scrollBar.UpdateBarSize(scrollWidth, scrollHeight, viewportWidth, viewportHeight);
```

### Updating Scroll Position

Update the scroll bar position to reflect content scrolling:

```cpp
scrollBar.UpdateScrollPosition(Vector2(10.0f, 20.0f));
```

### Hiding the Bar

Programmatically hide the scroll bar. This only affects bars with `ScrollBarVisibility::Auto`; always-visible bars remain visible:

```cpp
scrollBar.HideBar();
```

## Using with ScrollView

`ScrollView` has built-in scroll bar support. Configure visibility directly on the `ScrollView` instance:

```cpp
#include <dali-ui-foundation/public-api/scroll-view.h>

ScrollView scrollView = ScrollView::New()
  .SetScrollDirection(ScrollDirection::Vertical)
  .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
  .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);
```

When using `ScrollView`, the scroll bar is managed automatically and you do not need to create a separate `ScrollBar` instance or call `UpdateBarSize()` manually.