---
title: Scroll Bar
sidebar_label: Scroll Bar
category: views-components
---

# Scroll Bar

`Dali::Ui::ScrollBar` displays vertical and horizontal scroll indicators for scrollable content in dali-ui applications.

## Table of Contents

- [Create a Scroll Bar](#create-a-scroll-bar)
- [Control Scroll Bar Visibility](#control-scroll-bar-visibility)
- [Style the Bar](#style-the-bar)
- [Update Size and Position](#update-size-and-position)
- [Read Back State](#read-back-state)

## Create a Scroll Bar

Create a scroll bar with `Dali::Ui::ScrollBar::New()`. A default-constructed `Dali::Ui::ScrollBar` is an uninitialized handle; use `New()` when you need an initialized component.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void CreateScrollBar()
{
  Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
}
```

`Dali::Ui::ScrollBar::GetTarget()` returns the scroll bar itself as a `Dali::Ui::View`. Use this when helper code expects a `Dali::Ui::View` app-facing object.

```cpp
void UseAsView(Dali::Ui::ScrollBar scrollBar)
{
  Dali::Ui::View targetView = scrollBar.GetTarget();
}
```

If your code receives a generic handle from another layer, use `Dali::Ui::ScrollBar::DownCast()` to recover a `Dali::Ui::ScrollBar` handle.

```cpp
void ConfigureFromHandle(Dali::BaseHandle handle)
{
  Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::DownCast(handle);

  if(scrollBar)
  {
    scrollBar.SetBarThickness(8.0f);
  }
}
```

## Control Scroll Bar Visibility

`Dali::Ui::ScrollBar` has independent visibility modes for vertical and horizontal indicators.

- `Dali::Ui::ScrollBarVisibility::Auto` shows the bar while scrolling.
- `Dali::Ui::ScrollBarVisibility::Always` keeps the bar visible.
- `Dali::Ui::ScrollBarVisibility::Never` removes that bar.

```cpp
void ConfigureVerticalScrolling(Dali::Ui::ScrollBar scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
}
```

Use the matching getters when state must be mirrored in application UI.

```cpp
bool UsesVerticalIndicator(Dali::Ui::ScrollBar scrollBar)
{
  return scrollBar.GetVerticalScrollBarVisibility() != Dali::Ui::ScrollBarVisibility::Never;
}
```

`Dali::Ui::ScrollBar::HideBar()` fades out bars that use `Dali::Ui::ScrollBarVisibility::Auto`. It is useful when your own scrolling controller knows that scrolling has ended.

```cpp
void OnScrollFinished(Dali::Ui::ScrollBar scrollBar)
{
  scrollBar.HideBar();
}
```

## Style the Bar

Use typed setters to style the indicator without raw property access. The default bar thickness is `4.0f`, the default offset is `2.0f`, the default minimum size is `1.0f`, the default corner radius is `Vector4(1.0f, 1.0f, 1.0f, 1.0f)`, and the default color is `Vector4(0.8f, 0.8f, 0.8f, 1.0f)`.

```cpp
void StyleScrollBar(Dali::Ui::ScrollBar scrollBar)
{
  scrollBar.SetBarThickness(10.0f);
  scrollBar.SetBarOffset(6.0f);
  scrollBar.SetBarMinSize(32.0f);
  scrollBar.SetBarColor(Dali::Vector4(0.16f, 0.18f, 0.22f, 1.0f));
  scrollBar.SetBarCornerRadius(5.0f);
}
```

`Dali::Ui::ScrollBar::SetBarCornerRadius(float)` applies the same radius to all corners. `Dali::Ui::ScrollBar::SetBarCornerRadius(const Dali::Vector4&)` sets each corner individually, and `Dali::Ui::ScrollBar::GetBarCornerRadius()` returns the four corner values as a `Dali::Vector4`.

```cpp
Dali::Vector4 ReadCornerRadius(Dali::Ui::ScrollBar scrollBar)
{
  scrollBar.SetBarCornerRadius(3.0f);
  return scrollBar.GetBarCornerRadius();
}
```

## Update Size and Position

Call `Dali::Ui::ScrollBar::UpdateBarSize()` when the scrollable area or viewport size changes. The method receives scrollable width, scrollable height, viewport width, and viewport height.

```cpp
void UpdateForContentSize(Dali::Ui::ScrollBar scrollBar)
{
  const float scrollWidth = 1200.0f;
  const float scrollHeight = 2400.0f;
  const float viewportWidth = 600.0f;
  const float viewportHeight = 800.0f;

  scrollBar.UpdateBarSize(scrollWidth, scrollHeight, viewportWidth, viewportHeight);
}
```

Call `Dali::Ui::ScrollBar::UpdateScrollPosition()` or `Dali::Ui::ScrollBar::SetScrollPosition()` as the content position changes. Both accept a `Dali::Vector2`.

```cpp
void SyncScrollPosition(Dali::Ui::ScrollBar scrollBar, float scrollX, float scrollY)
{
  Dali::Vector2 position(scrollX, scrollY);

  scrollBar.UpdateScrollPosition(position);
}
```

`Dali::Ui::ScrollBar::SetScrollPosition()` stores the position and updates the bar position.

```cpp
void JumpToTop(Dali::Ui::ScrollBar scrollBar)
{
  scrollBar.SetScrollPosition(Dali::Vector2(0.0f, 0.0f));
}
```

## Read Back State

The getter methods are useful for preserving style and behavior when a view is recreated.

```cpp
struct ScrollBarStyle
{
  float         thickness;
  float         minSize;
  float         offset;
  Dali::Vector4 color;
  Dali::Vector4 cornerRadius;
};

ScrollBarStyle CaptureStyle(Dali::Ui::ScrollBar scrollBar)
{
  ScrollBarStyle style;
  style.thickness = scrollBar.GetBarThickness();
  style.minSize = scrollBar.GetBarMinSize();
  style.offset = scrollBar.GetBarOffset();
  style.color = scrollBar.GetBarColor();
  style.cornerRadius = scrollBar.GetBarCornerRadius();
  return style;
}
```

You can also read the current scroll position and visibility settings.

```cpp
void CopyScrollBarState(Dali::Ui::ScrollBar source, Dali::Ui::ScrollBar destination)
{
  destination.SetScrollPosition(source.GetScrollPosition());
  destination.SetVerticalScrollBarVisibility(source.GetVerticalScrollBarVisibility());
  destination.SetHorizontalScrollBarVisibility(source.GetHorizontalScrollBarVisibility());
}
```
