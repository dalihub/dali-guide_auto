---
title: I Scroll Bar
sidebar_label: I Scroll Bar
category: views-components
---

# I Scroll Bar

`Dali::Ui::IScrollBar` is the dali-ui interface for controlling scroll bar visibility, size, position, and fade behavior for a `Dali::Ui::View`-based scroll target.

## Table of Contents

- [Use the Scroll Bar Interface](#use-the-scroll-bar-interface)
- [Configure Vertical and Horizontal Visibility](#configure-vertical-and-horizontal-visibility)
- [Synchronize Bar Size with Content and Viewport](#synchronize-bar-size-with-content-and-viewport)
- [Update Scroll Position During Scrolling](#update-scroll-position-during-scrolling)
- [Hide Auto Scroll Bars When Scrolling Ends](#hide-auto-scroll-bars-when-scrolling-ends)
- [Read the Target View](#read-the-target-view)

## Use the Scroll Bar Interface

Use `Dali::Ui::IScrollBar` when application code needs to configure a scroll bar without depending on the concrete `Dali::Ui::ScrollBar` handle. The interface is `View`-oriented: `GetTarget()` returns the target as `Dali::Ui::View`, so app code can stay in the dali-ui object model.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void ConfigureScrollBar(Dali::Ui::IScrollBar& scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
}
```

`Dali::Ui::ScrollBar` is the concrete dali-ui scroll bar class that implements `Dali::Ui::IScrollBar`. Use it as a concrete handle only when setup code needs an actual scroll bar object; keep reusable application logic typed to `Dali::Ui::IScrollBar&`.

```cpp
#include <dali-ui-foundation/public-api/scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void ApplyScrollBarPolicy(Dali::Ui::ScrollBar scrollBar)
{
  Dali::Ui::IScrollBar& bar = scrollBar;

  bar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Always);
  bar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
}
```

## Configure Vertical and Horizontal Visibility

`SetVerticalScrollBarVisibility()` and `SetHorizontalScrollBarVisibility()` control each axis independently. Use `Dali::Ui::ScrollBarVisibility::Auto` when the bar should appear during scrolling, `Dali::Ui::ScrollBarVisibility::Always` when it should remain visible, and `Dali::Ui::ScrollBarVisibility::Never` when that axis should not show a bar.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void UseVerticalOnlyScrollIndicator(Dali::Ui::IScrollBar& scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
}
```

You can query the current policy with `GetVerticalScrollBarVisibility()` and `GetHorizontalScrollBarVisibility()` before changing it.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void EnsureHorizontalBarIsDisabled(Dali::Ui::IScrollBar& scrollBar)
{
  if(scrollBar.GetHorizontalScrollBarVisibility() != Dali::Ui::ScrollBarVisibility::Never)
  {
    scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
  }
}
```

## Synchronize Bar Size with Content and Viewport

Call `UpdateBarSize()` when the scrollable content size or visible viewport size changes. The four arguments are the scrollable width, scrollable height, viewport width, and viewport height.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void OnScrollableGeometryChanged(Dali::Ui::IScrollBar& scrollBar)
{
  const float scrollWidth    = 1440.0f;
  const float scrollHeight   = 2400.0f;
  const float viewportWidth  = 720.0f;
  const float viewportHeight = 1280.0f;

  scrollBar.UpdateBarSize(scrollWidth, scrollHeight, viewportWidth, viewportHeight);
}
```

For a vertical-only area, still pass both dimensions so the scroll bar can evaluate both axes consistently.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void ConfigureArticleScrollBar(Dali::Ui::IScrollBar& scrollBar, float contentHeight, float viewportHeight)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);

  const float contentWidth  = 0.0f;
  const float viewportWidth = 0.0f;

  scrollBar.UpdateBarSize(contentWidth, contentHeight, viewportWidth, viewportHeight);
}
```

## Update Scroll Position During Scrolling

Call `UpdateScrollPosition()` with the current scroll offset. The method takes a `Dali::Vector2`, where the x and y components represent the current horizontal and vertical scroll positions.

```cpp
#include <dali/public-api/math/vector2.h>
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void OnScrollMoved(Dali::Ui::IScrollBar& scrollBar, float scrollX, float scrollY)
{
  scrollBar.UpdateScrollPosition(Dali::Vector2(scrollX, scrollY));
}
```

A scroll handler usually updates the bar size when layout changes, then updates the position as the user scrolls.

```cpp
#include <dali/public-api/math/vector2.h>
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void RefreshScrollBar(Dali::Ui::IScrollBar& scrollBar,
                      float contentWidth,
                      float contentHeight,
                      float viewportWidth,
                      float viewportHeight,
                      const Dali::Vector2& scrollPosition)
{
  scrollBar.UpdateBarSize(contentWidth, contentHeight, viewportWidth, viewportHeight);
  scrollBar.UpdateScrollPosition(scrollPosition);
}
```

## Hide Auto Scroll Bars When Scrolling Ends

`HideBar()` fades out scroll bars that use `Dali::Ui::ScrollBarVisibility::Auto`. It is intended for the end of a scrolling interaction.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void OnScrollFinished(Dali::Ui::IScrollBar& scrollBar)
{
  if(scrollBar.GetVerticalScrollBarVisibility() == Dali::Ui::ScrollBarVisibility::Auto ||
     scrollBar.GetHorizontalScrollBarVisibility() == Dali::Ui::ScrollBarVisibility::Auto)
  {
    scrollBar.HideBar();
  }
}
```

If an axis uses `Dali::Ui::ScrollBarVisibility::Always`, keep the visibility policy unchanged and use `HideBar()` only as part of scroll-end behavior.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void KeepPersistentVerticalBar(Dali::Ui::IScrollBar& scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Always);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
}

void FinishHorizontalScroll(Dali::Ui::IScrollBar& scrollBar)
{
  scrollBar.HideBar();
}
```

## Read the Target View

`GetTarget()` returns the `Dali::Ui::View` associated with the scroll bar. Use it when application code needs to connect scroll bar state to the owning view-level model.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/view.h>

Dali::Ui::View GetScrolledView(Dali::Ui::IScrollBar& scrollBar)
{
  return scrollBar.GetTarget();
}
```

Keep target-aware code expressed in terms of `Dali::Ui::View`. This avoids raw actor-level patterns and keeps the scroll bar integration aligned with dali-ui application code.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/view.h>

void CacheScrollTarget(Dali::Ui::IScrollBar& scrollBar, Dali::Ui::View& target)
{
  target = scrollBar.GetTarget();
}
```
