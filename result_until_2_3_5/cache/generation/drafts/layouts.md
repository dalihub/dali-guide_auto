---
title: Layouts
sidebar_label: Layouts
category: layout-sizing
---

# Layouts

`Dali::Ui` layouts let application code arrange `Dali::Ui::View` children with absolute, flex, grid, or stack sizing rules.

## Table of Contents

- [Choosing a Layout Container](#choosing-a-layout-container)
- [Absolute Placement](#absolute-placement)
- [Flexible Rows and Columns](#flexible-rows-and-columns)
- [Grid Tracks](#grid-tracks)
- [Typed Layout Parameters](#typed-layout-parameters)

## Choosing a Layout Container

Use `Dali::Ui::Layout` as the common layout base and choose a concrete layout for the sizing model you need:

- `Dali::Ui::AbsoluteLayout` positions child views from explicit bounds.
- `Dali::Ui::FlexLayout` distributes child views along a main axis, with optional wrapping and alignment.
- `Dali::Ui::GridLayout` arranges child views into row and column tracks.
- `Dali::Ui::StackLayout` stacks child views in one direction.

Each layout is a `Dali::Ui::View`, so it can be used as a parent view in a dali-ui view tree.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::AbsoluteLayout overlayLayer = Dali::Ui::AbsoluteLayout::New();
Dali::Ui::FlexLayout     toolbar      = Dali::Ui::FlexLayout::New();
Dali::Ui::GridLayout     dashboard    = Dali::Ui::GridLayout::New();
Dali::Ui::StackLayout    panelStack   = Dali::Ui::StackLayout::New();

Dali::Ui::Layout genericLayout = Dali::Ui::Layout::DownCast(toolbar);
Dali::Ui::FlexLayout sameFlex  = Dali::Ui::FlexLayout::DownCast(genericLayout);
```

`Dali::Ui::LayoutParams` is the common base for per-child parameters. Use the matching typed parameter handle for the parent layout instead of storing layout data in untyped properties.

```cpp
Dali::Ui::LayoutParams absoluteParams =
  Dali::Ui::AbsoluteLayoutParams::New()
    .SetX(24.0f)
    .SetY(16.0f)
    .SetWidth(160.0f)
    .SetHeight(48.0f);

Dali::Ui::LayoutParams flexParams =
  Dali::Ui::FlexLayoutParams::New()
    .SetFlexGrow(1.0f)
    .SetFlexShrink(1.0f);
```

## Absolute Placement

`Dali::Ui::AbsoluteLayout` is useful for overlays, fixed-position controls, and custom compositions where each child view needs explicit layout bounds. Create it with `Dali::Ui::AbsoluteLayout::New`, then describe each child with `Dali::Ui::AbsoluteLayoutParams`.

Use `Dali::Ui::AbsoluteLayoutParams::SetX`, `Dali::Ui::AbsoluteLayoutParams::SetY`, `Dali::Ui::AbsoluteLayoutParams::SetWidth`, and `Dali::Ui::AbsoluteLayoutParams::SetHeight` for direct bounds.

```cpp
Dali::Ui::AbsoluteLayout canvas = Dali::Ui::AbsoluteLayout::New();

Dali::Ui::AbsoluteLayoutParams badgeParams =
  Dali::Ui::AbsoluteLayoutParams::New()
    .SetX(32.0f)
    .SetY(24.0f)
    .SetWidth(96.0f)
    .SetHeight(32.0f);

float badgeX      = badgeParams.GetX();
float badgeY      = badgeParams.GetY();
float badgeWidth  = badgeParams.GetWidth();
float badgeHeight = badgeParams.GetHeight();
```

For a single rectangle assignment, use `Dali::Ui::LayoutRect` with `Dali::Ui::AbsoluteLayoutParams::SetBounds`.

```cpp
Dali::Ui::LayoutRect contentBounds(12.0f, 20.0f, 240.0f, 160.0f);

Dali::Ui::AbsoluteLayoutParams contentParams =
  Dali::Ui::AbsoluteLayoutParams::New()
    .SetBounds(contentBounds);

Dali::Ui::LayoutRect resolvedBounds = contentParams.GetBounds();
```

`Dali::Ui::AbsoluteLayoutParams::SetFlags` controls whether bounds are interpreted proportionally. For example, `Dali::Ui::AbsoluteLayoutFlags::SIZE_PROPORTIONAL` makes width and height proportional to the parent content area.

```cpp
Dali::Ui::AbsoluteLayoutParams fillQuarter =
  Dali::Ui::AbsoluteLayoutParams::New()
    .SetBounds(Dali::Ui::LayoutRect(0.0f, 0.0f, 0.5f, 0.5f))
    .SetFlags(Dali::Ui::AbsoluteLayoutFlags::SIZE_PROPORTIONAL);

Dali::Ui::AbsoluteLayoutFlags flags = fillQuarter.GetFlags();
```

## Flexible Rows and Columns

`Dali::Ui::FlexLayout` arranges child views with flexbox-style direction, wrapping, justification, and alignment. Configure the container with typed setters such as `Dali::Ui::FlexLayout::SetDirection`, `Dali::Ui::FlexLayout::SetWrap`, `Dali::Ui::FlexLayout::SetJustifyContent`, `Dali::Ui::FlexLayout::SetAlignItems`, and `Dali::Ui::FlexLayout::SetAlignContent`.

```cpp
Dali::Ui::FlexLayout MakeActionRow(Dali::Ui::FlexDirection direction,
                                   Dali::Ui::FlexWrap wrap,
                                   Dali::Ui::FlexJustify justify,
                                   Dali::Ui::FlexAlign alignItems,
                                   Dali::Ui::FlexAlign alignContent)
{
  return Dali::Ui::FlexLayout::New()
    .SetDirection(direction)
    .SetWrap(wrap)
    .SetJustifyContent(justify)
    .SetAlignItems(alignItems)
    .SetAlignContent(alignContent);
}
```

Use `Dali::Ui::FlexLayoutParams` for per-child flex behavior. `Dali::Ui::FlexLayoutParams::SetFlexBasis` sets the initial main-axis size, `Dali::Ui::FlexLayoutParams::SetFlexGrow` assigns remaining positive space, and `Dali::Ui::FlexLayoutParams::SetFlexShrink` controls how the child shrinks when space is tight.

```cpp
Dali::Ui::FlexLayoutParams titleParams =
  Dali::Ui::FlexLayoutParams::New()
    .SetFlexBasis(180.0f)
    .SetFlexGrow(1.0f)
    .SetFlexShrink(1.0f);

Dali::Ui::FlexLayoutParams fixedButtonParams =
  Dali::Ui::FlexLayoutParams::New()
    .SetFlexBasis(96.0f)
    .SetFlexGrow(0.0f)
    .SetFlexShrink(0.0f);

float titleBasis  = titleParams.GetFlexBasis();
float titleGrow   = titleParams.GetFlexGrow();
float titleShrink = titleParams.GetFlexShrink();
```

A child can override the parent cross-axis alignment with `Dali::Ui::FlexLayoutParams::SetAlignSelf`.

```cpp
Dali::Ui::FlexLayoutParams centeredItem =
  Dali::Ui::FlexLayoutParams::New()
    .SetFlexBasis(120.0f)
    .SetAlignSelf(Dali::Ui::FlexAlign::CENTER);

Dali::Ui::FlexAlign alignSelf = centeredItem.GetAlignSelf();
```

## Grid Tracks

`Dali::Ui::GridLayout` uses row and column definitions to create grid tracks. Add tracks with `Dali::Ui::GridLayout::AddRowDefinition` and `Dali::Ui::GridLayout::AddColumnDefinition`.

```cpp
Dali::Ui::GridLayout grid = Dali::Ui::GridLayout::New();

grid.AddRowDefinition(Dali::Ui::GridLength::Absolute(64.0f));
grid.AddRowDefinition(Dali::Ui::GridLength::Star(1.0f));

grid.AddColumnDefinition(Dali::Ui::GridLength::Absolute(160.0f));
grid.AddColumnDefinition(Dali::Ui::GridLength::Star(2.0f));
grid.AddColumnDefinition(Dali::Ui::GridLength::Auto());

uint32_t rowCount    = grid.GetRowCount();
uint32_t columnCount = grid.GetColumnCount();
```

Use `Dali::Ui::GridLayout::GetRowDefinitions` and `Dali::Ui::GridLayout::GetColumnDefinitions` when code needs to inspect the configured tracks.

```cpp
Dali::Vector<Dali::Ui::GridLength> rows    = grid.GetRowDefinitions();
Dali::Vector<Dali::Ui::GridLength> columns = grid.GetColumnDefinitions();

float firstColumnValue = columns[0].GetValue();
Dali::Ui::GridLengthType firstColumnType = columns[0].GetType();
```

Clear definitions when rebuilding a grid for a new layout mode.

```cpp
grid.ClearRowDefinitions();
grid.ClearColumnDefinitions();

grid.AddRowDefinition(Dali::Ui::GridLength::Auto());
grid.AddColumnDefinition(Dali::Ui::GridLength::Star());
```

## Typed Layout Parameters

`Dali::Ui::AbsoluteLayoutParams`, `Dali::Ui::FlexLayoutParams`, and `Dali::Ui::GridLayoutParams` are owned application-facing APIs for describing how a child `Dali::Ui::View` participates in its parent layout.

Use `DownCast` when retrieving a generic handle and then working with the concrete parameter type.

```cpp
Dali::Ui::LayoutParams genericParams =
  Dali::Ui::FlexLayoutParams::New()
    .SetFlexGrow(1.0f)
    .SetFlexShrink(1.0f);

Dali::Ui::FlexLayoutParams flexParams =
  Dali::Ui::FlexLayoutParams::DownCast(genericParams);

float grow = flexParams.GetFlexGrow();
```

Use copy construction through the matching `New` overload when multiple children need similar parameters with small variations.

```cpp
Dali::Ui::AbsoluteLayoutParams baseOverlay =
  Dali::Ui::AbsoluteLayoutParams::New()
    .SetWidth(128.0f)
    .SetHeight(48.0f);

Dali::Ui::AbsoluteLayoutParams topLeft =
  Dali::Ui::AbsoluteLayoutParams::New(baseOverlay)
    .SetX(16.0f)
    .SetY(16.0f);

Dali::Ui::AbsoluteLayoutParams bottomLeft =
  Dali::Ui::AbsoluteLayoutParams::New(baseOverlay)
    .SetX(16.0f)
    .SetY(240.0f);
```

For grid children, `Dali::Ui::GridLayoutParams` identifies the target row and column, plus optional span and alignment.

```cpp
Dali::Ui::GridLayoutParams headerParams =
  Dali::Ui::GridLayoutParams::New()
    .SetRow(0u)
    .SetColumn(0u)
    .SetColumnSpan(3u)
    .SetHorizontalAlignment(Dali::Ui::LayoutAlignment::FILL)
    .SetVerticalAlignment(Dali::Ui::LayoutAlignment::CENTER);

uint32_t row        = headerParams.GetRow();
uint32_t column     = headerParams.GetColumn();
uint32_t columnSpan = headerParams.GetColumnSpan();
```
