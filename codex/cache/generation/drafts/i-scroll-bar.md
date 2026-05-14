---
title: I Scroll Bar
sidebar_label: I Scroll Bar
category: views-components
---

# I Scroll Bar

`Dali::Ui::IScrollBar` is the dali-ui interface for controlling scroll bar visibility, size, position, and fade-out behavior for a `Dali::Ui::View`.

## Table of Contents

- [Using an `IScrollBar` Handle](#using-an-iscrollbar-handle)
- [Configuring Bar Visibility](#configuring-bar-visibility)
- [Synchronizing Size and Scroll Position](#synchronizing-size-and-scroll-position)
- [Accessing the Target View](#accessing-the-target-view)
- [Hiding Auto Bars When Scrolling Ends](#hiding-auto-bars-when-scrolling-ends)

## Using an `IScrollBar` Handle

`Dali::Ui::IScrollBar` is an interface. Application code normally works with it through a concrete dali-ui component that owns or exposes a scroll bar. Keep your application-facing code typed to `Dali::Ui::IScrollBar&` when you only need scroll bar behavior.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void ConfigureScrollBars(Dali::Ui::IScrollBar& scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
}
```

`Dali::Ui::ScrollBar` is the concrete dali-ui scroll bar type, but `Dali::Ui::IScrollBar` is the useful contract for app code that configures scroll bar behavior without depending on the concrete implementation.

```cpp
#include <dali-ui-foundation/public-api/scroll-bar.h>

void ConfigureConcreteScrollBar(Dali::Ui::ScrollBar& scrollBar)
{
  Dali::Ui::IScrollBar& interface = scrollBar;

  interface.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Always);
  interface.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
}
```

## Configuring Bar Visibility

`Dali::Ui::IScrollBar` controls the vertical and horizontal bars independently. Use `SetVerticalScrollBarVisibility()` and `SetHorizontalScrollBarVisibility()` with `Dali::Ui::ScrollBarVisibility`.

Available visibility values are:

- `Dali::Ui::ScrollBarVisibility::Auto`: show the bar while scrolling.
- `Dali::Ui::ScrollBarVisibility::Always`: keep the bar visible.
- `Dali::Ui::ScrollBarVisibility::Never`: remove or suppress the bar.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void UseVerticalOnlyScrollBar(Dali::Ui::IScrollBar& scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
}
```

Read back the current policy with `GetVerticalScrollBarVisibility()` and `GetHorizontalScrollBarVisibility()` when your UI state needs to reflect the active setting.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

bool IsVerticalBarPinned(const Dali::Ui::IScrollBar& scrollBar)
{
  return scrollBar.GetVerticalScrollBarVisibility() ==
         Dali::Ui::ScrollBarVisibility::Always;
}

bool IsHorizontalBarDisabled(const Dali::Ui::IScrollBar& scrollBar)
{
  return scrollBar.GetHorizontalScrollBarVisibility() ==
         Dali::Ui::ScrollBarVisibility::Never;
}
```

## Synchronizing Size and Scroll Position

Call `UpdateBarSize()` when the scrollable area or viewport size changes. The arguments are the full scrollable width and height followed by the visible viewport width and height.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void OnScrollableGeometryChanged(Dali::Ui::IScrollBar& scrollBar,
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

Call `UpdateScrollPosition()` when the current scroll offset changes. The position is passed as a `Dali::Vector2`.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali/public-api/math/vector2.h>

void OnScrolled(Dali::Ui::IScrollBar& scrollBar, float x, float y)
{
  scrollBar.UpdateScrollPosition(Dali::Vector2(x, y));
}
```

For a typical vertical list, update the bar size when content or viewport dimensions change, then update the scroll position whenever the list scroll offset changes.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali/public-api/math/vector2.h>

void RefreshVerticalScrollState(Dali::Ui::IScrollBar& scrollBar,
                                float contentHeight,
                                float viewportHeight,
                                float scrollY)
{
  scrollBar.UpdateBarSize(0.0f, contentHeight, 0.0f, viewportHeight);
  scrollBar.UpdateScrollPosition(Dali::Vector2(0.0f, scrollY));
}
```

## Accessing the Target View

`GetTarget()` returns the `Dali::Ui::View` associated with the scroll bar. Use it when application code needs the app-facing view handle while still keeping scroll bar operations behind the `Dali::Ui::IScrollBar` interface.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/view.h>

Dali::Ui::View GetScrollBarTarget(const Dali::Ui::IScrollBar& scrollBar)
{
  return scrollBar.GetTarget();
}
```

This keeps the object model in the dali-ui layer: application code works with `Dali::Ui::View`, not raw actor handles.

## Hiding Auto Bars When Scrolling Ends

`HideBar()` requests a fade-out of the scroll bar. It is intended for the end of scrolling and only affects bars using `Dali::Ui::ScrollBarVisibility::Auto`.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void FinishScrollInteraction(Dali::Ui::IScrollBar& scrollBar)
{
  if(scrollBar.GetVerticalScrollBarVisibility() == Dali::Ui::ScrollBarVisibility::Auto ||
     scrollBar.GetHorizontalScrollBarVisibility() == Dali::Ui::ScrollBarVisibility::Auto)
  {
    scrollBar.HideBar();
  }
}
```

For bars configured as `Dali::Ui::ScrollBarVisibility::Always`, keep the visibility policy unchanged and avoid using `HideBar()` as a general visibility toggle. Use `SetVerticalScrollBarVisibility()` or `SetHorizontalScrollBarVisibility()` when the policy itself needs to change.