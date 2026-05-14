---
title: Scroll Bar
sidebar_label: Scroll Bar
category: views-components
---

# Scroll Bar

The `ScrollBar` control provides visual feedback for scroll position within scrollable content. It can be used standalone or integrated with `ScrollView` to indicate the current viewport position relative to the total content size.

## Table of Contents

- [Creating a Scroll Bar](#creating-a-scroll-bar)
- [Configuring Bar Appearance](#configuring-bar-appearance)
- [Controlling Visibility](#controlling-visibility)
- [Managing Scroll Position](#managing-scroll-position)
- [Using with ScrollView](#using-with-scrollview)

## Creating a Scroll Bar

Create a `ScrollBar` instance using the static `New()` method. The scroll bar inherits from `AbsoluteLayout`, allowing you to position and size it within your view hierarchy.

```cpp
#include <dali-ui-foundation/public-api/scroll-bar.h>

using namespace Dali::Ui;

// Create a new scroll bar
ScrollBar scrollBar = ScrollBar::New();

// Add to your view hierarchy
parentView.Add(scrollBar);
```

You can downcast a `BaseHandle` to a `ScrollBar` using `DownCast()`:

```cpp
BaseHandle handle = scrollBar;
ScrollBar casted = ScrollBar::DownCast(handle);

if (casted)
{
  // Successfully downcasted
}
```

## Configuring Bar Appearance

The `ScrollBar` provides several properties to customize its visual appearance.

### Bar Thickness

Set the width of the scroll bar using `SetBarThickness()`. The default thickness is 4.0f.

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Set a thicker scroll bar
scrollBar.SetBarThickness(12.0f);

// Get current thickness
float thickness = scrollBar.GetBarThickness(); // Returns 12.0f
```

### Bar Color

Customize the scroll bar color using `SetBarColor()`. The default color is a light gray (0.8, 0.8, 0.8, 1.0).

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Set a blue scroll bar
scrollBar.SetBarColor(Vector4(0.0f, 0.0f, 1.0f, 1.0f));

// Get current color
Vector4 color = scrollBar.GetBarColor();
```

### Bar Corner Radius

Apply rounded corners to the scroll bar using `SetBarCornerRadius()`. You can set a uniform radius for all corners or specify individual values.

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Set uniform corner radius for all corners
scrollBar.SetBarCornerRadius(3.0f);

// Set individual corner radii (topLeft, topRight, bottomRight, bottomLeft)
scrollBar.SetBarCornerRadius(Vector4(2.0f, 4.0f, 2.0f, 4.0f));

// Get current corner radius
Vector4 radius = scrollBar.GetBarCornerRadius();
```

### Bar Offset

Control the gap between the scroll bar and the container edges using `SetBarOffset()`. The default offset is 2.0f.

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Set a larger offset from edges
scrollBar.SetBarOffset(5.0f);

// Get current offset
float offset = scrollBar.GetBarOffset(); // Returns 5.0f
```

### Minimum Bar Size

Ensure the scroll bar remains visible even with large content by setting a minimum size using `SetBarMinSize()`.

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Ensure bar is always at least 20 pixels
scrollBar.SetBarMinSize(20.0f);

// Get minimum size
float minSize = scrollBar.GetBarMinSize();
```

## Controlling Visibility

The `ScrollBar` supports three visibility modes defined by the `ScrollBarVisibility` enum:

| Value | Description |
|-------|-------------|
| `ScrollBarVisibility::Auto` | Shows the scroll bar only during scrolling (default) |
| `ScrollBarVisibility::Always` | Always shows the scroll bar |
| `ScrollBarVisibility::Never` | Never shows the scroll bar |

Control vertical and horizontal scroll bar visibility independently:

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Always show vertical scroll bar
scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Always);

// Never show horizontal scroll bar
scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);

// Query current visibility
ScrollBarVisibility vVisibility = scrollBar.GetVerticalScrollBarVisibility();
ScrollBarVisibility hVisibility = scrollBar.GetHorizontalScrollBarVisibility();
```

To hide the scroll bar programmatically:

```cpp
scrollBar.HideBar();
```

## Managing Scroll Position

Update the scroll bar position to reflect the current scroll state of your content.

### Setting Scroll Position

Use `SetScrollPosition()` to update the scroll bar indicator position:

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Set scroll position
scrollBar.SetScrollPosition(Vector2(10.0f, 25.0f));

// Get current scroll position
Vector2 position = scrollBar.GetScrollPosition();
```

### Updating Bar Size

Synchronize the scroll bar thumb size with the content and viewport dimensions using `UpdateBarSize()`:

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Update bar size based on content and viewport dimensions
// scrollWidth: total scrollable content width
// scrollHeight: total scrollable content height
// viewportWidth: visible area width
// viewportHeight: visible area height
scrollBar.UpdateBarSize(1000.0f, 2000.0f, 400.0f, 600.0f);
```

### Updating Scroll Position from External Source

Use `UpdateScrollPosition()` to update the scroll position from an external source:

```cpp
ScrollBar scrollBar = ScrollBar::New();

// Update scroll position from external scroll events
scrollBar.UpdateScrollPosition(Vector2(50.0f, 100.0f));
```

## Using with ScrollView

The `ScrollView` component has built-in scroll bar support. Configure scroll bar visibility directly on `ScrollView` for common use cases:

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

// Create a ScrollView with automatic scroll bars
ScrollView scrollView = ScrollView::New()
  .SetScrollDirection(ScrollDirection::Vertical)
  .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
  .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);

// Add content to the scroll view
View content = View::New();
content.SetRequestedWidth(600.0f);
content.SetRequestedHeight(2000.0f);
scrollView.SetContent(content);

// Add scroll view to parent
parentView.Add(scrollView);
```

For standalone scroll bar usage, create and configure a `ScrollBar` instance separately:

```cpp
ScrollBar scrollBar = ScrollBar::New();
scrollBar.SetBarThickness(12.0f);
scrollBar.SetBarColor(Vector4(0.0f, 0.5f, 1.0f, 1.0f));
scrollBar.SetBarCornerRadius(6.0f);
scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto);
scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);