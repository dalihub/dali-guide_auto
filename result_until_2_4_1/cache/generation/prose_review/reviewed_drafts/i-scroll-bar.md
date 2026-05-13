---
title: I Scroll Bar
sidebar_label: I Scroll Bar
category: views-components
---

# I Scroll Bar

`Dali::Ui::IScrollBar` is the dali-ui interface for keeping a scroll indicator synchronized with a scrollable `Dali::Ui::View`.

## Table of Contents

- [Use `IScrollBar` from a dali-ui View](#use-iscrollbar-from-a-dali-ui-view)
- [Choose vertical and horizontal visibility](#choose-vertical-and-horizontal-visibility)
- [Update the bar when content or viewport size changes](#update-the-bar-when-content-or-viewport-size-changes)
- [Synchronize the bar with scroll position](#synchronize-the-bar-with-scroll-position)
- [Hide automatic bars after scrolling ends](#hide-automatic-bars-after-scrolling-ends)

## Use `IScrollBar` from a dali-ui View

`Dali::Ui::IScrollBar` is an interface. Application code normally works with an object that implements this interface and keeps view-facing code in terms of `Dali::Ui::View`, not raw actors. Use `Dali::Ui::IScrollBar::GetTarget` when code needs the app-facing view returned by the scroll bar implementation.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/view.h>

void InspectScrollBarTarget(Dali::Ui::IScrollBar& scrollBar)
{
  Dali::Ui::View targetView = scrollBar.GetTarget();

  if(targetView)
  {
    // Keep application logic View-based.
  }
}
```

`Dali::Ui::ScrollBar` is the public concrete scroll bar type that implements `Dali::Ui::IScrollBar`. Use the interface for reusable application code, and create or configure the concrete type where your view construction code needs a scroll bar view.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scroll-bar.h>

void ConfigureAnyScrollBar(Dali::Ui::IScrollBar& scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
}
```

## Choose vertical and horizontal visibility

`Dali::Ui::IScrollBar` has separate visibility settings for vertical and horizontal bars. Use `Dali::Ui::IScrollBar::SetVerticalScrollBarVisibility` and `Dali::Ui::IScrollBar::SetHorizontalScrollBarVisibility` to match the scroll direction of your view.

`Dali::Ui::ScrollBarVisibility::Auto` shows the bar while scrolling. `Dali::Ui::ScrollBarVisibility::Always` keeps it visible. `Dali::Ui::ScrollBarVisibility::Never` disables that axis.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void ConfigureVerticalListBar(Dali::Ui::IScrollBar& scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
}
```

You can read the current policy with `Dali::Ui::IScrollBar::GetVerticalScrollBarVisibility` and `Dali::Ui::IScrollBar::GetHorizontalScrollBarVisibility`.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

bool UsesPersistentVerticalBar(const Dali::Ui::IScrollBar& scrollBar)
{
  return scrollBar.GetVerticalScrollBarVisibility() == Dali::Ui::ScrollBarVisibility::Always;
}

bool HasHorizontalBarEnabled(const Dali::Ui::IScrollBar& scrollBar)
{
  return scrollBar.GetHorizontalScrollBarVisibility() != Dali::Ui::ScrollBarVisibility::Never;
}
```

## Update the bar when content or viewport size changes

Call `Dali::Ui::IScrollBar::UpdateBarSize` when the scrollable content size or visible viewport size changes. The arguments are the scrollable width, scrollable height, viewport width, and viewport height.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void OnScrollableGeometryChanged(Dali::Ui::IScrollBar& scrollBar,
                                 float contentWidth,
                                 float contentHeight,
                                 float viewportWidth,
                                 float viewportHeight)
{
  scrollBar.UpdateBarSize(contentWidth, contentHeight, viewportWidth, viewportHeight);
}
```

For a vertical list with no horizontal overflow, pass the viewport width for both the scrollable width and the viewport width. This records that the horizontal range fits while still giving the bar the dimensions it needs for vertical sizing.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void ConfigureVerticalContent(Dali::Ui::IScrollBar& scrollBar,
                              float contentHeight,
                              float viewportWidth,
                              float viewportHeight)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);

  scrollBar.UpdateBarSize(viewportWidth, contentHeight, viewportWidth, viewportHeight);
}
```

## Synchronize the bar with scroll position

Call `Dali::Ui::IScrollBar::UpdateScrollPosition` when the target view scroll position changes. The position is a `Dali::Vector2`, where `x` is the horizontal scroll offset and `y` is the vertical scroll offset.

```cpp
#include <dali/public-api/math/vector2.h>
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void OnScrollPositionChanged(Dali::Ui::IScrollBar& scrollBar, const Dali::Vector2& position)
{
  scrollBar.UpdateScrollPosition(position);
}
```

For vertical-only content, keep `x` at `0.0f` and pass the vertical offset as `y`.

```cpp
#include <dali/public-api/math/vector2.h>
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void UpdateVerticalOffset(Dali::Ui::IScrollBar& scrollBar, float scrollY)
{
  scrollBar.UpdateScrollPosition(Dali::Vector2(0.0f, scrollY));
}
```

For two-axis content, pass both offsets from the same scroll state that drives the target `Dali::Ui::View`.

```cpp
#include <dali/public-api/math/vector2.h>
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void UpdateTwoAxisOffset(Dali::Ui::IScrollBar& scrollBar, float scrollX, float scrollY)
{
  Dali::Vector2 position(scrollX, scrollY);
  scrollBar.UpdateScrollPosition(position);
}
```

## Hide automatic bars after scrolling ends

`Dali::Ui::IScrollBar::HideBar` fades out bars that use `Dali::Ui::ScrollBarVisibility::Auto`. Call it when your own scroll interaction has finished and you want automatic bars to hide immediately.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void OnScrollGestureFinished(Dali::Ui::IScrollBar& scrollBar)
{
  if(scrollBar.GetVerticalScrollBarVisibility() == Dali::Ui::ScrollBarVisibility::Auto ||
     scrollBar.GetHorizontalScrollBarVisibility() == Dali::Ui::ScrollBarVisibility::Auto)
  {
    scrollBar.HideBar();
  }
}
```

A typical manual scroll bar update path configures visibility once, updates size when layout changes, updates position during scrolling, and hides automatic bars at the end of the interaction.

```cpp
#include <dali/public-api/math/vector2.h>
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

class ScrollBarController
{
public:
  explicit ScrollBarController(Dali::Ui::IScrollBar& scrollBar)
  : mScrollBar(scrollBar)
  {
    mScrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
    mScrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
  }

  void OnLayout(float contentHeight, float viewportWidth, float viewportHeight)
  {
    mScrollBar.UpdateBarSize(viewportWidth, contentHeight, viewportWidth, viewportHeight);
  }

  void OnScroll(float scrollY)
  {
    mScrollBar.UpdateScrollPosition(Dali::Vector2(0.0f, scrollY));
  }

  void OnScrollFinished()
  {
    mScrollBar.HideBar();
  }

private:
  Dali::Ui::IScrollBar& mScrollBar;
};
```
