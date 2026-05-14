-----BEGIN_REVISED_MARKDOWN-----
---
title: I Scroll Bar
sidebar_label: I Scroll Bar
category: views-components
---

# I Scroll Bar

`IScrollBar` defines the interface for displaying and controlling scroll bars in dali-ui applications. The `ScrollBar` class provides the concrete implementation.

## Table of Contents

- [Creating a Scroll Bar](#creating-a-scroll-bar)
- [Configuring Scroll Bar Visibility](#configuring-scroll-bar-visibility)
- [Updating Scroll Bar State](#updating-scroll-bar-state)
- [Styling the Scroll Bar](#styling-the-scroll-bar)

## Creating a Scroll Bar

Create a `ScrollBar` using the static `ScrollBar::New()` method. The `ScrollBar` class inherits from `AbsoluteLayout` and implements the `IScrollBar` interface.

```cpp
#include <dali-ui-foundation/public-api/scroll-bar.h>

using namespace Dali::Ui;

ScrollBar scrollBar = ScrollBar::New();
```

You can downcast a handle to `ScrollBar` using `ScrollBar::DownCast()`:

```cpp
BaseHandle handle = scrollBar;
ScrollBar scrollBar2 = ScrollBar::DownCast(handle);
```

## Configuring Scroll Bar Visibility

The `IScrollBar` interface provides methods to control the visibility of vertical and horizontal scroll bars independently. Use the `ScrollBarVisibility` enum to set the visibility mode.

### Visibility Modes

| Value | Description |
|-------|-------------|
| `ScrollBarVisibility::Auto` | Show scroll bar only when scrolling |
| `ScrollBarVisibility::Always` | Always show the scroll bar |
| `ScrollBarVisibility::Never` | Never show the scroll bar |

### Setting Visibility

```cpp
// Set vertical scroll bar to always visible
scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Always);

// Set horizontal scroll bar to auto mode
scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Auto);
```

### Getting Current Visibility

```cpp
ScrollBarVisibility verticalVisibility = scrollBar.GetVerticalScrollBarVisibility();
ScrollBarVisibility horizontalVisibility = scrollBar.GetHorizontalScrollBarVisibility();
```

By default, both scroll bars use `ScrollBarVisibility::Auto`.

## Updating Scroll Bar State

When the scrollable content or viewport changes, update the scroll bar to reflect the current state.

### Updating Bar Size

Call `UpdateBarSize()` to recalculate the scroll bar thumb size based on the scrollable area and viewport dimensions:

```cpp
float scrollWidth = 500.0f;    // Total scrollable content width
float scrollHeight = 1000.0f;  // Total scrollable content height
float viewportWidth = 200.0f;  // Visible viewport width
float viewportHeight = 300.0f; // Visible viewport height

scrollBar.UpdateBarSize(scrollWidth, scrollHeight, viewportWidth, viewportHeight);
```

The scroll bar uses these values to calculate the thumb size proportionally. A smaller viewport relative to the content produces a smaller thumb.

### Updating Scroll Position

Call `UpdateScrollPosition()` to move the scroll bar thumb to reflect the current scroll position:

```cpp
Vector2 scrollPosition(50.0f, 100.0f);
scrollBar.UpdateScrollPosition(scrollPosition);
```

### Hiding the Scroll Bar

Call `HideBar()` to fade out the scroll bar when scrolling ends. This method only affects scroll bars with `ScrollBarVisibility::Auto`:

```cpp
scrollBar.HideBar();
```

## Styling the Scroll Bar

The `ScrollBar` class provides additional properties to customize the appearance of the scroll bar.

### Bar Thickness

Set or get the thickness of the scroll bar:

```cpp
// Set thickness
scrollBar.SetBarThickness(10.0f);

// Get current thickness
float thickness = scrollBar.GetBarThickness();  // Default: 4.0f
```

### Bar Color

Set or get the color of the scroll bar using a `Vector4` (RGBA):

```cpp
// Set color (red)
Vector4 color(1.0f, 0.0f, 0.0f, 1.0f);
scrollBar.SetBarColor(color);

// Get current color
Vector4 currentColor = scrollBar.GetBarColor();  // Default: (0.8, 0.8, 0.8, 1.0)
```

### Bar Minimum Size

Set a minimum size for the scroll bar thumb to ensure it remains visible even with large content:

```cpp
scrollBar.SetBarMinSize(20.0f);
float minSize = scrollBar.GetBarMinSize();
```

### Bar Offset

Set the offset of the scroll bar from the container edges:

```cpp
scrollBar.SetBarOffset(5.0f);
float offset = scrollBar.GetBarOffset();  // Default: 2.0f
```

### Bar Corner Radius

Set rounded corners for the scroll bar. You can set the same radius for all corners or individual radii for each corner:

```cpp
// Same radius for all corners
scrollBar.SetBarCornerRadius(3.0f);

// Individual radii (topLeft, topRight, bottomRight, bottomLeft)
Vector4 radii(1.0f, 2.0f, 3.0f, 4.0f);
scrollBar.SetBarCornerRadius(radii);

// Get current corner radii
Vector4 currentRadii = scrollBar.GetBarCornerRadius();  // Default: (1.0, 1.0, 1.0, 1.0)
```

### Scroll Position

Get or set the current scroll position directly:

```cpp
Vector2 position(15.0f, 25.0f);
scrollBar.SetScrollPosition(position);
Vector2 currentPosition = scrollBar.GetScrollPosition();  // Default: (0.0, 0.0)
```
-----END_REVISED_MARKDOWN-----

-----BEGIN_PROSE_REVIEW_REPORT-----
# I Scroll Bar Prose Review

## Summary

The draft was reviewed against the following source files:
- `repos/dali-ui/dali-ui-foundation/public-api/i-scroll-bar.h` (IScrollBar interface)
- `repos/dali-ui/dali-ui-foundation/public-api/scroll-bar.h` (ScrollBar concrete class)
- `repos/dali-ui/dali-ui-foundation/public-api/scrollable-enum.h` (ScrollBarVisibility enum)
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-ScrollBar.cpp` (UTC tests)

## Findings

### Accuracy Verification

| Section | Status | Notes |
|---------|--------|-------|
| Overview | ✅ Accurate | Correctly describes IScrollBar as interface and ScrollBar as concrete implementation |
| Creating a Scroll Bar | ✅ Accurate | `ScrollBar::New()` and `ScrollBar::DownCast()` verified in header and UTC |
| Visibility Modes | ✅ Accurate | Enum values `Auto`, `Always`, `Never` match `scrollable-enum.h` exactly |
| Default Visibility | ✅ Accurate | UTC confirms default is `ScrollBarVisibility::Auto` for both directions |
| UpdateBarSize | ✅ Accurate | Parameter names and descriptions match header documentation |
| UpdateScrollPosition | ✅ Accurate | Method signature matches header |
| HideBar | ✅ Accurate | "Only affects bars with Auto visibility" matches header docstring |
| Bar Thickness | ✅ Accurate | Default 4.0f confirmed by UTC |
| Bar Color | ✅ Accurate | Default (0.8, 0.8, 0.8, 1.0) confirmed by UTC |
| Bar Min Size | ✅ Accurate | API exists, no default tested in UTC |
| Bar Offset | ✅ Accurate | Default 2.0f confirmed by UTC |
| Bar Corner Radius | ✅ Accurate | Default (1.0, 1.0, 1.0, 1.0) confirmed by UTC; both overloads verified |
| Scroll Position | ✅ Accurate | Default (0.0, 0.0) confirmed by UTC |

### Changes Made

**No changes required.** The draft prose accurately reflects the public API surface, default values, and behavior as documented in the headers and verified by UTC tests.

### Not Documented (Intentional Omissions)

- `IScrollBar::GetTarget()`: This method returns the target view for which the scroll bar is responsible. It is a getter typically used internally or for introspection. Not including it in the application developer guide is acceptable since the primary use case is configuring scroll bars, not querying their targets.

### Remaining Concerns

None. The draft is well-structured, accurate, and follows the dali-ui application developer guide conventions.

## Conclusion

The draft passes source-grounded prose review without modifications. All API names, default values, method signatures, and behavioral descriptions are verified against public headers and UTC tests.
-----END_PROSE_REVIEW_REPORT-----