---
title: Scroll Bar
sidebar_label: Scroll Bar
category: views-components
---

# Scroll Bar

`Dali::Ui::ScrollBar` displays and updates scroll bar indicators for scrollable dali-ui content.

## Table of Contents

- [Create a Scroll Bar](#create-a-scroll-bar)
- [Choose Scroll Bar Visibility](#choose-scroll-bar-visibility)
- [Style the Bar](#style-the-bar)
- [Update Size and Position](#update-size-and-position)
- [Read Current State](#read-current-state)
- [Hide Auto Bars](#hide-auto-bars)

## Create a Scroll Bar

Create a scroll bar with `Dali::Ui::ScrollBar::New`. The default constructor `Dali::Ui::ScrollBar::ScrollBar` creates an uninitialized handle, so application code should use `Dali::Ui::ScrollBar::New` when it needs a usable scroll bar.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void CreateScrollBar()
{
  ScrollBar scrollBar = ScrollBar::New();

  scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);
}
```

Use `Dali::Ui::ScrollBar` as a dali-ui view component. Its scroll target is exposed as `Dali::Ui::View` through `Dali::Ui::ScrollBar::GetTarget`, so app-facing code can stay in the `Dali::Ui::View` object model.

```cpp
void InspectTarget(ScrollBar scrollBar)
{
  View target = scrollBar.GetTarget();

  if(target)
  {
    Vector2 currentPosition = scrollBar.GetScrollPosition();
    scrollBar.SetScrollPosition(currentPosition);
  }
}
```

## Choose Scroll Bar Visibility

`Dali::Ui::ScrollBar::SetVerticalScrollBarVisibility` and `Dali::Ui::ScrollBar::SetHorizontalScrollBarVisibility` control whether each axis is shown automatically, always shown, or never shown.

Use `ScrollBarVisibility::Auto` for bars that appear while scrolling, `ScrollBarVisibility::Always` for persistent indicators, and `ScrollBarVisibility::Never` for disabled axes.

```cpp
void ConfigureVerticalOnlyScrollBar(ScrollBar scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);
}
```

For a two-axis scroll area, enable both axes explicitly.

```cpp
void ConfigureBothAxes(ScrollBar scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Always);
  scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Always);
}
```

The current policy can be read back with `Dali::Ui::ScrollBar::GetVerticalScrollBarVisibility` and `Dali::Ui::ScrollBar::GetHorizontalScrollBarVisibility`.

```cpp
bool UsesVerticalAutoVisibility(ScrollBar scrollBar)
{
  return scrollBar.GetVerticalScrollBarVisibility() == ScrollBarVisibility::Auto;
}
```

## Style the Bar

The visual bar can be configured with typed setters. Use `Dali::Ui::ScrollBar::SetBarThickness` for bar width, `Dali::Ui::ScrollBar::SetBarColor` for color, `Dali::Ui::ScrollBar::SetBarMinSize` for the smallest displayed thumb size, and `Dali::Ui::ScrollBar::SetBarOffset` for the gap from the scroll bar container edges.

```cpp
void StyleScrollBar(ScrollBar scrollBar)
{
  scrollBar.SetBarThickness(10.0f);
  scrollBar.SetBarColor(Vector4(0.2f, 0.45f, 0.9f, 1.0f));
  scrollBar.SetBarMinSize(24.0f);
  scrollBar.SetBarOffset(4.0f);
}
```

Use `Dali::Ui::ScrollBar::SetBarCornerRadius` to round the bar. The float overload applies the same radius to all corners.

```cpp
void MakeRoundedBar(ScrollBar scrollBar)
{
  scrollBar.SetBarCornerRadius(3.0f);

  Vector4 radius = scrollBar.GetBarCornerRadius();
  scrollBar.SetBarCornerRadius(radius.x);
}
```

You can read style values back with `Dali::Ui::ScrollBar::GetBarThickness`, `Dali::Ui::ScrollBar::GetBarColor`, `Dali::Ui::ScrollBar::GetBarMinSize`, `Dali::Ui::ScrollBar::GetBarOffset`, and `Dali::Ui::ScrollBar::GetBarCornerRadius`.

```cpp
float CalculateInsetForBar(ScrollBar scrollBar)
{
  return scrollBar.GetBarThickness() + scrollBar.GetBarOffset();
}
```

## Update Size and Position

Call `Dali::Ui::ScrollBar::UpdateBarSize` when the scrollable content size or viewport size changes. The method takes scrollable width, scrollable height, viewport width, and viewport height.

```cpp
void OnScrollableLayoutChanged(ScrollBar scrollBar)
{
  const float scrollWidth = 1200.0f;
  const float scrollHeight = 2400.0f;
  const float viewportWidth = 600.0f;
  const float viewportHeight = 800.0f;

  scrollBar.UpdateBarSize(scrollWidth, scrollHeight, viewportWidth, viewportHeight);
}
```

Use `Dali::Ui::ScrollBar::SetScrollPosition` when the application sets the current scroll position directly. Use `Dali::Ui::ScrollBar::UpdateScrollPosition` when synchronizing the bar from a scrolling component.

```cpp
void SyncScrollPosition(ScrollBar scrollBar, const Vector2& position)
{
  scrollBar.SetScrollPosition(position);
  scrollBar.UpdateScrollPosition(position);
}
```

The position value can be retrieved with `Dali::Ui::ScrollBar::GetScrollPosition`.

```cpp
Vector2 MoveDownByPage(ScrollBar scrollBar, float pageHeight)
{
  Vector2 position = scrollBar.GetScrollPosition();
  position.y += pageHeight;

  scrollBar.SetScrollPosition(position);
  return scrollBar.GetScrollPosition();
}
```

## Read Current State

A `Dali::Ui::ScrollBar` exposes its current configuration through matching getters. This is useful when a view state needs to be restored, compared, or copied to another scroll bar.

```cpp
void CopyVisualSettings(ScrollBar source, ScrollBar target)
{
  target.SetBarThickness(source.GetBarThickness());
  target.SetBarColor(source.GetBarColor());
  target.SetBarMinSize(source.GetBarMinSize());
  target.SetBarOffset(source.GetBarOffset());

  Vector4 radius = source.GetBarCornerRadius();
  target.SetBarCornerRadius(radius);
}
```

Visibility and position can be copied in the same way.

```cpp
void CopyScrollPolicy(ScrollBar source, ScrollBar target)
{
  target.SetVerticalScrollBarVisibility(source.GetVerticalScrollBarVisibility());
  target.SetHorizontalScrollBarVisibility(source.GetHorizontalScrollBarVisibility());
  target.SetScrollPosition(source.GetScrollPosition());
}
```

## Hide Auto Bars

`Dali::Ui::ScrollBar::HideBar` hides the bar using the scroll bar's fade-out behavior. It is intended for bars that use `ScrollBarVisibility::Auto`, where the bar is visible during scrolling and hidden when scrolling ends.

```cpp
void FinishScrolling(ScrollBar scrollBar)
{
  scrollBar.UpdateScrollPosition(scrollBar.GetScrollPosition());
  scrollBar.HideBar();
}
```

For a bar that should remain visible, use `Dali::Ui::ScrollBar::SetVerticalScrollBarVisibility` or `Dali::Ui::ScrollBar::SetHorizontalScrollBarVisibility` with `ScrollBarVisibility::Always` instead of hiding it at the end of interaction.

```cpp
void KeepIndicatorVisible(ScrollBar scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(ScrollBarVisibility::Always);
  scrollBar.SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);
}
```