---
title: Scroll Bar
sidebar_label: Scroll Bar
category: views-components
---

# Scroll Bar

`Dali::Ui::ScrollBar` displays scroll position and scrollable extent for a dali-ui view-based scrolling surface.

## Table of Contents

- [Create and Configure a Scroll Bar](#create-and-configure-a-scroll-bar)
- [Control Bar Visibility](#control-bar-visibility)
- [Synchronize Size and Scroll Position](#synchronize-size-and-scroll-position)
- [Style the Bar](#style-the-bar)
- [Read State and Reuse Handles](#read-state-and-reuse-handles)

## Create and Configure a Scroll Bar

Use `Dali::Ui::ScrollBar::New` to create an initialized `Dali::Ui::ScrollBar`. The scroll bar is a dali-ui object, so application code should treat its target as a `Dali::Ui::View` model rather than working through raw actor property calls.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);

scrollBar.SetBarThickness(8.0f);
scrollBar.SetBarMinSize(24.0f);
scrollBar.SetBarOffset(2.0f);
scrollBar.SetBarColor(Dali::Vector4(0.15f, 0.15f, 0.15f, 0.85f));
scrollBar.SetBarCornerRadius(4.0f);
```

`Dali::Ui::ScrollBar::GetTarget` returns the app-facing `Dali::Ui::View` represented by the scroll bar. This is useful when surrounding dali-ui code expects a view handle.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();
Dali::Ui::View scrollBarView = scrollBar.GetTarget();
```

## Control Bar Visibility

A scroll bar has independent vertical and horizontal visibility. Use `Dali::Ui::ScrollBar::SetVerticalScrollBarVisibility` and `Dali::Ui::ScrollBar::SetHorizontalScrollBarVisibility` with `Dali::Ui::ScrollBarVisibility`.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);

Dali::Ui::ScrollBarVisibility vertical = scrollBar.GetVerticalScrollBarVisibility();
Dali::Ui::ScrollBarVisibility horizontal = scrollBar.GetHorizontalScrollBarVisibility();
```

`Dali::Ui::ScrollBarVisibility::Auto` shows the bar during scroll updates, `Dali::Ui::ScrollBarVisibility::Always` keeps it visible, and `Dali::Ui::ScrollBarVisibility::Never` removes that axis from display.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Always);
scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
```

To hide an auto-visible bar when scrolling ends, call `Dali::Ui::ScrollBar::HideBar`.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
scrollBar.UpdateScrollPosition(Dali::Vector2(0.0f, 120.0f));
scrollBar.HideBar();
```

## Synchronize Size and Scroll Position

Call `Dali::Ui::ScrollBar::UpdateBarSize` when the scrollable content size or viewport size changes. The arguments are scrollable width, scrollable height, viewport width, and viewport height.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

const float scrollWidth = 640.0f;
const float scrollHeight = 1800.0f;
const float viewportWidth = 640.0f;
const float viewportHeight = 480.0f;

scrollBar.UpdateBarSize(scrollWidth, scrollHeight, viewportWidth, viewportHeight);
```

Use `Dali::Ui::ScrollBar::UpdateScrollPosition` when the associated scrolling view reports a new position. Use `Dali::Ui::ScrollBar::SetScrollPosition` when the scroll bar state is being set directly.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.UpdateBarSize(640.0f, 1800.0f, 640.0f, 480.0f);
scrollBar.UpdateScrollPosition(Dali::Vector2(0.0f, 320.0f));

Dali::Vector2 currentPosition = scrollBar.GetScrollPosition();
```

For direct state restoration, set the scroll position after sizing the bar.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.UpdateBarSize(1024.0f, 2400.0f, 1024.0f, 600.0f);
scrollBar.SetScrollPosition(Dali::Vector2(0.0f, 600.0f));
```

## Style the Bar

`Dali::Ui::ScrollBar` exposes typed setters for common visual details. Use `Dali::Ui::ScrollBar::SetBarThickness` for bar width, `Dali::Ui::ScrollBar::SetBarColor` for color, `Dali::Ui::ScrollBar::SetBarMinSize` for the minimum thumb size, and `Dali::Ui::ScrollBar::SetBarOffset` for the edge gap.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.SetBarThickness(10.0f);
scrollBar.SetBarMinSize(32.0f);
scrollBar.SetBarOffset(4.0f);
scrollBar.SetBarColor(Dali::Vector4(0.0f, 0.45f, 0.85f, 1.0f));
```

Use `Dali::Ui::ScrollBar::SetBarCornerRadius` to round all corners with one value. `Dali::Ui::ScrollBar::GetBarCornerRadius` returns a `Dali::Vector4` containing top-left, top-right, bottom-right, and bottom-left radius values.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.SetBarCornerRadius(6.0f);

Dali::Vector4 radius = scrollBar.GetBarCornerRadius();
```

Read back values with the matching getters when synchronizing a design setting panel or preserving scroll bar configuration.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.SetBarThickness(12.0f);
scrollBar.SetBarOffset(3.0f);
scrollBar.SetBarMinSize(28.0f);

float thickness = scrollBar.GetBarThickness();
float offset = scrollBar.GetBarOffset();
float minSize = scrollBar.GetBarMinSize();
Dali::Vector4 color = scrollBar.GetBarColor();
```

## Read State and Reuse Handles

A default-constructed `Dali::Ui::ScrollBar` is an uninitialized handle. Assign an initialized handle from `Dali::Ui::ScrollBar::New` before using member functions.

```cpp
Dali::Ui::ScrollBar scrollBar;

scrollBar = Dali::Ui::ScrollBar::New();
scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
```

Use move assignment when transferring ownership-style handle variables in application code.

```cpp
Dali::Ui::ScrollBar CreateConfiguredScrollBar()
{
  Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

  scrollBar.SetBarThickness(8.0f);
  scrollBar.SetBarColor(Dali::Vector4(0.2f, 0.2f, 0.2f, 1.0f));
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);

  return scrollBar;
}

Dali::Ui::ScrollBar activeScrollBar;
activeScrollBar = CreateConfiguredScrollBar();
```

When a generic handle is available from framework code, use `Dali::Ui::ScrollBar::DownCast` to recover a `Dali::Ui::ScrollBar` handle before calling scroll-bar-specific APIs.

```cpp
Dali::Ui::ScrollBar original = Dali::Ui::ScrollBar::New();
Dali::BaseHandle handle(original);

Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::DownCast(handle);
scrollBar.SetScrollPosition(Dali::Vector2(0.0f, 160.0f));
```
