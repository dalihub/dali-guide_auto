---
title: Scroll Bar
sidebar_label: Scroll Bar
category: views-components
---

# Scroll Bar

`Dali::Ui::ScrollBar` displays scroll position for a `dali-ui` view and can be configured with typed setters for visibility, size, color, offset, and position.

## Table of Contents

- [Create and Style a Scroll Bar](#create-and-style-a-scroll-bar)
- [Control Vertical and Horizontal Visibility](#control-vertical-and-horizontal-visibility)
- [Synchronize Size and Scroll Position](#synchronize-size-and-scroll-position)
- [Use Handles in View-Based Code](#use-handles-in-view-based-code)

## Create and Style a Scroll Bar

Create a scroll bar with `Dali::Ui::ScrollBar::New`. The scroll bar exposes typed configuration methods, so application code can avoid raw property writes for common styling.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

ScrollBar CreateStyledScrollBar()
{
  ScrollBar scrollBar = ScrollBar::New();

  scrollBar.SetBarThickness(10.0f);
  scrollBar.SetBarMinSize(24.0f);
  scrollBar.SetBarOffset(4.0f);
  scrollBar.SetBarCornerRadius(3.0f);
  scrollBar.SetBarColor(Vector4(0.18f, 0.42f, 0.86f, 1.0f));

  return scrollBar;
}
```

Use the corresponding getters when you need to preserve or inspect the current configuration before applying another style.

```cpp
void IncreaseScrollBarEmphasis(ScrollBar scrollBar)
{
  const float currentThickness = scrollBar.GetBarThickness();
  const float currentMinSize   = scrollBar.GetBarMinSize();
  const float currentOffset    = scrollBar.GetBarOffset();
  const Vector4 currentColor   = scrollBar.GetBarColor();
  const Vector4 currentRadius  = scrollBar.GetBarCornerRadius();

  scrollBar.SetBarThickness(currentThickness + 2.0f);
  scrollBar.SetBarMinSize(currentMinSize + 4.0f);
  scrollBar.SetBarOffset(currentOffset);
  scrollBar.SetBarColor(currentColor);
  scrollBar.SetBarCornerRadius(currentRadius.x);
}
```

## Control Vertical and Horizontal Visibility

`Dali::Ui::ScrollBarVisibility` has three modes: `ScrollBarVisibility::Auto`, `ScrollBarVisibility::Always`, and `ScrollBarVisibility::Never`. Use `Dali::Ui::ScrollBar::SetVerticalScrollBarVisibility` and `Dali::Ui::ScrollBar::SetHorizontalScrollBarVisibility` to configure each axis independently.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali::Ui;

void ConfigureVerticalOnlyScrollBar(ScrollBar scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);
}
```

Read the current axis modes with `Dali::Ui::ScrollBar::GetVerticalScrollBarVisibility` and `Dali::Ui::ScrollBar::GetHorizontalScrollBarVisibility`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali::Ui;

void ForceVisibleWhenEitherAxisIsAlways(ScrollBar scrollBar)
{
  const ScrollBarVisibility verticalVisibility = scrollBar.GetVerticalScrollBarVisibility();
  const ScrollBarVisibility horizontalVisibility = scrollBar.GetHorizontalScrollBarVisibility();

  if(verticalVisibility == ScrollBarVisibility::Always ||
     horizontalVisibility == ScrollBarVisibility::Always)
  {
    scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Always);
    scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Always);
  }
}
```

## Synchronize Size and Scroll Position

When a `Dali::Ui::ScrollBar` is used directly, call `Dali::Ui::ScrollBar::UpdateBarSize` with the scrollable content size and the viewport size. The arguments are `scrollWidth`, `scrollHeight`, `viewportWidth`, and `viewportHeight`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void UpdateScrollMetrics(ScrollBar scrollBar)
{
  const float contentWidth  = 1200.0f;
  const float contentHeight = 2400.0f;
  const float viewportWidth = 600.0f;
  const float viewportHeight = 800.0f;

  scrollBar.UpdateBarSize(contentWidth, contentHeight, viewportWidth, viewportHeight);
}
```

Use `Dali::Ui::ScrollBar::SetScrollPosition` or `Dali::Ui::ScrollBar::UpdateScrollPosition` when the visible content position changes. `Dali::Ui::ScrollBar::GetScrollPosition` returns the stored position as a `Dali::Vector2`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void MoveScrollThumb(ScrollBar scrollBar)
{
  scrollBar.SetScrollPosition(Vector2(0.0f, 320.0f));

  const Vector2 currentPosition = scrollBar.GetScrollPosition();

  scrollBar.UpdateScrollPosition(Vector2(currentPosition.x, currentPosition.y + 120.0f));
}
```

For auto-visible bars, call `Dali::Ui::ScrollBar::HideBar` when the scroll interaction has ended.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali::Ui;

void FinishScrollInteraction(ScrollBar scrollBar)
{
  scrollBar.HideBar();
}
```

## Use Handles in View-Based Code

`Dali::Ui::ScrollBar` is the app-facing scroll bar object. When code needs the associated view handle, `Dali::Ui::ScrollBar::GetTarget` returns a `Dali::Ui::View`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali::Ui;

View GetScrollBarView(ScrollBar scrollBar)
{
  return scrollBar.GetTarget();
}
```

Use `Dali::Ui::ScrollBar::DownCast` when receiving a generic handle from surrounding infrastructure and you need to recover a scroll bar handle.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

ScrollBar AsScrollBar(BaseHandle handle)
{
  ScrollBar scrollBar = ScrollBar::DownCast(handle);

  if(scrollBar)
  {
    scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto);
    scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);
  }

  return scrollBar;
}
```
