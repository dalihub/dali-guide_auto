---
title: I Scroll Bar
sidebar_label: I Scroll Bar
category: views-components
---

# I Scroll Bar

`Dali::Ui::IScrollBar` is the dali-ui interface for updating scroll-bar visibility, size, and position for a `Dali::Ui::View` target.

## Table of Contents

- [Use `IScrollBar` as the Scroll-Bar Contract](#use-iscrollbar-as-the-scroll-bar-contract)
- [Configure Axis Visibility](#configure-axis-visibility)
- [Synchronize Size and Position](#synchronize-size-and-position)
- [Hide Automatic Bars When Scrolling Ends](#hide-automatic-bars-when-scrolling-ends)
- [Read the Target View](#read-the-target-view)

## Use `IScrollBar` as the Scroll-Bar Contract

`Dali::Ui::IScrollBar` is an interface. Application code can work with an object that implements it, then drive the scroll-bar state through the interface methods. Keep the app-facing target model as `Dali::Ui::View`; avoid coupling scroll-bar logic to lower-level actor APIs.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

namespace Ui = Dali::Ui;

void ConfigureScrollBar(Ui::IScrollBar& scrollBar,
                        Ui::ScrollBarVisibility verticalVisibility,
                        Ui::ScrollBarVisibility horizontalVisibility)
{
  scrollBar.SetVerticalScrollBarVisibility(verticalVisibility);
  scrollBar.SetHorizontalScrollBarVisibility(horizontalVisibility);
}
```

The interface owns the core scroll-bar operations:

- `GetTarget()` returns the `Dali::Ui::View` target associated with the scroll bar implementation.
- `SetVerticalScrollBarVisibility()` and `SetHorizontalScrollBarVisibility()` configure the two axes independently.
- `UpdateBarSize()` updates thumb sizing from content and viewport dimensions.
- `UpdateScrollPosition()` updates the displayed scroll offset.
- `HideBar()` fades out bars whose visibility mode is `Dali::Ui::ScrollBarVisibility::Auto`.

## Configure Axis Visibility

The vertical and horizontal axes are configured separately. This lets a dali-ui component expose only the axes that matter for the current content model. Use `Dali::Ui::ScrollBarVisibility::Auto` to show a bar while scrolling, `Dali::Ui::ScrollBarVisibility::Always` to keep it visible, or `Dali::Ui::ScrollBarVisibility::Never` to remove it.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

namespace Ui = Dali::Ui;

void ApplyAxisVisibility(Ui::IScrollBar& scrollBar,
                         Ui::ScrollBarVisibility vertical,
                         Ui::ScrollBarVisibility horizontal)
{
  scrollBar.SetVerticalScrollBarVisibility(vertical);
  scrollBar.SetHorizontalScrollBarVisibility(horizontal);
}
```

You can read the configured values back with `GetVerticalScrollBarVisibility()` and `GetHorizontalScrollBarVisibility()` when a component needs to preserve or mirror the current policy.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

namespace Ui = Dali::Ui;

void CopyVisibilityPolicy(const Ui::IScrollBar& source, Ui::IScrollBar& destination)
{
  const Ui::ScrollBarVisibility vertical = source.GetVerticalScrollBarVisibility();
  const Ui::ScrollBarVisibility horizontal = source.GetHorizontalScrollBarVisibility();

  destination.SetVerticalScrollBarVisibility(vertical);
  destination.SetHorizontalScrollBarVisibility(horizontal);
}
```

## Synchronize Size and Position

Call `UpdateBarSize()` when the scrollable content size or viewport size changes. The method takes the full scrollable width and height first, followed by the visible viewport width and height.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

namespace Ui = Dali::Ui;

void OnScrollMetricsChanged(Ui::IScrollBar& scrollBar,
                            float contentWidth,
                            float contentHeight,
                            float viewportWidth,
                            float viewportHeight)
{
  scrollBar.UpdateBarSize(contentWidth, contentHeight, viewportWidth, viewportHeight);
}
```

Call `UpdateScrollPosition()` when the scroll offset changes. The position is passed as a `Dali::Vector2`.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali/public-api/math/vector2.h>

namespace Ui = Dali::Ui;

void OnScrolled(Ui::IScrollBar& scrollBar, float scrollX, float scrollY)
{
  scrollBar.UpdateScrollPosition(Dali::Vector2(scrollX, scrollY));
}
```

A typical scroll update path updates the bar size after layout or content-size changes, then updates the current scroll position during user interaction or programmatic scrolling.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali/public-api/math/vector2.h>

namespace Ui = Dali::Ui;

void RefreshScrollBar(Ui::IScrollBar& scrollBar,
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

## Hide Automatic Bars When Scrolling Ends

`HideBar()` is intended for the end of a scroll interaction. It fades out bars whose visibility policy is `Dali::Ui::ScrollBarVisibility::Auto`.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

namespace Ui = Dali::Ui;

void OnScrollFinished(Ui::IScrollBar& scrollBar)
{
  scrollBar.HideBar();
}
```

If your component supports repeated scroll updates, keep `HideBar()` separate from `UpdateScrollPosition()`: update position while scrolling, then hide when the interaction finishes.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali/public-api/math/vector2.h>

namespace Ui = Dali::Ui;

void HandleScrollFrame(Ui::IScrollBar& scrollBar, const Dali::Vector2& position)
{
  scrollBar.UpdateScrollPosition(position);
}

void HandleScrollEnd(Ui::IScrollBar& scrollBar)
{
  scrollBar.HideBar();
}
```

## Read the Target View

Use `GetTarget()` when scroll-bar logic needs the `Dali::Ui::View` target exposed by the scroll-bar implementation. The returned object is a `Dali::Ui::View`, which keeps application code aligned with the dali-ui view model.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/view.h>

namespace Ui = Dali::Ui;

Ui::View GetScrollableView(const Ui::IScrollBar& scrollBar)
{
  return scrollBar.GetTarget();
}
```

This is useful when a higher-level component owns several view-related helpers and needs to identify the `Dali::Ui::View` associated with a particular `Dali::Ui::IScrollBar`.
