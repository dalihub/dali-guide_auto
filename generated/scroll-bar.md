---
title: Scroll Bar
sidebar_label: Scroll Bar
category: uncategorized
---

# Scroll Bar

The Scroll Bar component provides visual feedback for scrollable content position. It can be used standalone or integrated with scrollable views to indicate the current viewport position within larger content.

## Table of Contents

- [Creating a Scroll Bar](#creating-a-scroll-bar)
- [Configuring Bar Appearance](#configuring-bar-appearance)
- [Controlling Visibility](#controlling-visibility)
- [Managing Scroll Position](#managing-scroll-position)
- [Updating Bar Size and Position](#updating-bar-size-and-position)

## Creating a Scroll Bar

Create a `ScrollBar` instance using the static `New()` method. The component inherits from `AbsoluteLayout`, allowing you to position it within your view hierarchy.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

// Create a scroll bar
ScrollBar scrollBar = ScrollBar::New();
```

To safely cast a handle to `ScrollBar`, use `DownCast()`:

```cpp
BaseHandle handle(scrollBar);
ScrollBar castedScrollBar = ScrollBar::DownCast(handle);

if (castedScrollBar)
{
  // Successfully downcasted
}
```

## Configuring Bar Appearance

### Thickness

Set the scroll bar thickness using `SetBarThickness()`. The default thickness is 4.0f.

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Set a thicker scroll bar
scrollBar.SetBarThickness(12.0f);

// Get current thickness
float thickness = scrollBar.GetBarThickness(); // Returns 12.0f
```

### Color

Configure the scroll bar color with `SetBarColor()`. The default color is light gray (0.8, 0.8, 0.8, 1.0).

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Set scroll bar to blue
scrollBar.SetBarColor(Vector4(0.0f, 0.0f, 1.0f, 1.0f));

// Set scroll bar to semi-transparent red
scrollBar.SetBarColor(Vector4(1.0f, 0.0f, 0.0f, 0.7f));

// Get current color
Vector4 color = scrollBar.GetBarColor();
```

### Corner Radius

Apply rounded corners to the scroll bar using `SetBarCornerRadius()`. You can set a uniform radius for all corners or specify individual values.

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Set uniform corner radius for all corners
scrollBar.SetBarCornerRadius(3.0f);

// Set individual corner radii (topLeft, topRight, bottomRight, bottomLeft)
scrollBar.SetBarCornerRadius(Vector4(2.0f, 4.0f, 4.0f, 2.0f));

// Get current corner radii
Vector4 radii = scrollBar.GetBarCornerRadius();
```

### Minimum Size

Set the minimum size of the scroll bar thumb using `SetBarMinSize()`. This ensures the bar remains visible even when content is very large.

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Ensure minimum bar size of 20 pixels
scrollBar.SetBarMinSize(20.0f);

// Get current minimum size
float minSize = scrollBar.GetBarMinSize();
```

### Bar Offset

Set the offset distance from the scroll bar container edges using `SetBarOffset()`. This creates a small gap between the bar and the container. The default offset is 2.0f.

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Set bar offset from edges
scrollBar.SetBarOffset(5.0f);

// Get current offset
float offset = scrollBar.GetBarOffset();
```

## Controlling Visibility

Control the visibility of vertical and horizontal scroll bars independently using `ScrollBarVisibility` enum values:

- `ScrollBarVisibility::Auto` - Show scroll bar only when scrolling (default)
- `ScrollBarVisibility::Always` - Always show scroll bar
- `ScrollBarVisibility::Never` - Never show scroll bar

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Always show vertical scroll bar
scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Always);

// Never show horizontal scroll bar
scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);

// Query current visibility settings
ScrollBarVisibility vertVis = scrollBar.GetVerticalScrollBarVisibility();
ScrollBarVisibility horizVis = scrollBar.GetHorizontalScrollBarVisibility();
```

## Managing Scroll Position

Get and set the scroll position programmatically using `GetScrollPosition()` and `SetScrollPosition()`.

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Set scroll position
scrollBar.SetScrollPosition(Vector2(15.0f, 25.0f));

// Get current scroll position
Vector2 position = scrollBar.GetScrollPosition();
```

To hide the scroll bar programmatically:

```cpp
scrollBar.HideBar();
```

## Updating Bar Size and Position

When using `ScrollBar` with custom scrollable content, update the bar size and position to reflect the content and viewport dimensions.

### Updating Bar Size

Call `UpdateBarSize()` with the content size and viewport size to recalculate the scroll bar thumb proportions.

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Update bar size based on content and viewport dimensions
// Parameters: scrollWidth, scrollHeight, viewportWidth, viewportHeight
scrollBar.UpdateBarSize(100.0f, 500.0f, 50.0f, 100.0f);
```

### Updating Scroll Position

Call `UpdateScrollPosition()` to update the scroll bar thumb position based on the current scroll offset.

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Update scroll bar position based on content scroll position
scrollBar.UpdateScrollPosition(Vector2(10.0f, 20.0f));
```

### Integration with ScrollView

When using `ScrollView`, the scroll bar visibility can be configured directly on the scroll view:

```cpp
ScrollView scrollView = ScrollView::New()
  .SetScrollDirection(ScrollDirection::Vertical)
  .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
  .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);
