---
title: I Scroll Bar
sidebar_label: I Scroll Bar
category: views-components
---

# I Scroll Bar

`Dali::Ui::IScrollBar` is the dali-ui interface for keeping scroll bar visibility, size, and position in sync with a scrollable `Dali::Ui::View`.

## Table of Contents

- [Using `IScrollBar` from a View-Based Component](#using-iscrollbar-from-a-view-based-component)
- [Configuring Vertical and Horizontal Visibility](#configuring-vertical-and-horizontal-visibility)
- [Keeping the Bar Size in Sync with the Viewport](#keeping-the-bar-size-in-sync-with-the-viewport)
- [Updating Position During Scrolling](#updating-position-during-scrolling)
- [Accessing the Target View](#accessing-the-target-view)

## Using `IScrollBar` from a View-Based Component

Use `Dali::Ui::IScrollBar` as an app-facing contract when a scrollable dali-ui component exposes or owns a scroll bar. The interface works with `Dali::Ui::View` as the target object model, so application code does not need to manipulate a raw actor to drive scroll bar state.

`Dali::Ui::ScrollBar` is the concrete dali-ui scroll bar type, but application-level helper code can usually accept `Dali::Ui::IScrollBar&` and remain independent of the concrete implementation.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void PrepareScrollBar(Dali::Ui::IScrollBar& scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
}
```

`Dali::Ui::IScrollBar::~IScrollBar` is virtual, so an implementation can be used through the interface without changing the ownership model of the component that created it.

## Configuring Vertical and Horizontal Visibility

`Dali::Ui::IScrollBar` separates vertical and horizontal visibility. Use `SetVerticalScrollBarVisibility` for the vertical bar and `SetHorizontalScrollBarVisibility` for the horizontal bar.

The visibility values are:

- `Dali::Ui::ScrollBarVisibility::Auto`: show while scrolling.
- `Dali::Ui::ScrollBarVisibility::Always`: keep visible.
- `Dali::Ui::ScrollBarVisibility::Never`: do not show.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void UseVerticalOnlyScrollBar(Dali::Ui::IScrollBar& scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
}
```

Read back the current state with `GetVerticalScrollBarVisibility` and `GetHorizontalScrollBarVisibility` when your component needs to preserve or mirror the existing policy.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void MirrorScrollBarVisibility(Dali::Ui::IScrollBar& source,
                               Dali::Ui::IScrollBar& destination)
{
  destination.SetVerticalScrollBarVisibility(source.GetVerticalScrollBarVisibility());
  destination.SetHorizontalScrollBarVisibility(source.GetHorizontalScrollBarVisibility());
}
```

## Keeping the Bar Size in Sync with the Viewport

Call `UpdateBarSize` when the scrollable content size or viewport size changes. The arguments describe the total scrollable area first, then the visible viewport:

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void UpdateScrollMetrics(Dali::Ui::IScrollBar& scrollBar,
                         float contentWidth,
                         float contentHeight,
                         float viewportWidth,
                         float viewportHeight)
{
  scrollBar.UpdateBarSize(contentWidth,
                          contentHeight,
                          viewportWidth,
                          viewportHeight);
}
```

For a vertical list, the content height is usually larger than the viewport height, while the horizontal dimensions may match. Keeping both axes accurate lets the implementation decide whether a vertical bar, a horizontal bar, both bars, or no bar is needed.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void UpdateVerticalListBar(Dali::Ui::IScrollBar& scrollBar,
                           float listContentHeight,
                           float listViewportWidth,
                           float listViewportHeight)
{
  scrollBar.UpdateBarSize(listViewportWidth,
                          listContentHeight,
                          listViewportWidth,
                          listViewportHeight);
}
```

## Updating Position During Scrolling

Call `UpdateScrollPosition` whenever the scroll position changes. The position is passed as a `Dali::Vector2`, so a vertical-only scroller can keep the x component at `0.0f` and update the y component.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali/public-api/math/vector2.h>

void OnVerticalScrollChanged(Dali::Ui::IScrollBar& scrollBar, float scrollY)
{
  scrollBar.UpdateScrollPosition(Dali::Vector2(0.0f, scrollY));
}
```

For two-axis scrolling, pass both components.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali/public-api/math/vector2.h>

void OnScrollChanged(Dali::Ui::IScrollBar& scrollBar,
                     float scrollX,
                     float scrollY)
{
  scrollBar.UpdateScrollPosition(Dali::Vector2(scrollX, scrollY));
}
```

When scrolling ends, `HideBar` requests a fade-out for bars using automatic visibility. This is useful for app code that owns the scroll gesture lifecycle and wants `Auto` bars to disappear after interaction.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void OnScrollFinished(Dali::Ui::IScrollBar& scrollBar)
{
  scrollBar.HideBar();
}
```

## Accessing the Target View

`GetTarget` returns the `Dali::Ui::View` that the scroll bar is responsible for. Use it when a helper needs to inspect or pass along the target view while still treating the scroll bar through the `Dali::Ui::IScrollBar` interface.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/view.h>

Dali::Ui::View GetScrollableView(Dali::Ui::IScrollBar& scrollBar)
{
  return scrollBar.GetTarget();
}
```

A practical pattern is to keep layout and scroll metrics in the owning view component, then forward only the final visibility, size, and position values to `Dali::Ui::IScrollBar`.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>
#include <dali/public-api/math/vector2.h>

void SyncScrollBar(Dali::Ui::IScrollBar& scrollBar,
                   float contentWidth,
                   float contentHeight,
                   float viewportWidth,
                   float viewportHeight,
                   const Dali::Vector2& scrollPosition)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
  scrollBar.UpdateBarSize(contentWidth, contentHeight, viewportWidth, viewportHeight);
  scrollBar.UpdateScrollPosition(scrollPosition);
}
```
