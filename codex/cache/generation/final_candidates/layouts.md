---
title: Layouts
sidebar_label: Layouts
category: layout-sizing
---

# Layouts

`dali-ui` layout containers arrange `Dali::Ui::View` children with typed layout objects and typed per-child layout parameters.

## Table of Contents

- [Choosing a Layout](#choosing-a-layout)
- [Flex Layouts for Flowing Rows and Columns](#flex-layouts-for-flowing-rows-and-columns)
- [Absolute Layouts for Explicit Placement](#absolute-layouts-for-explicit-placement)
- [Grid Layouts for Track-Based Sizing](#grid-layouts-for-track-based-sizing)
- [Reading Layout State](#reading-layout-state)
- [Public Layout Handles and Integration Types](#public-layout-handles-and-integration-types)

## Choosing a Layout

Use `Dali::Ui::FlexLayout` when children should flow along a main axis, wrap, grow, or shrink. Use `Dali::Ui::AbsoluteLayout` when each child has explicit bounds. Use `Dali::Ui::GridLayout` when the container is divided into rows and columns.

`Dali::Ui::StackLayout` is the simple row-or-column stacking layout option, while `Dali::Ui::Layout` is the common public base class for layout containers and also derives from `Dali::Ui::View`. In application code, keep the view tree expressed as `Dali::Ui::View` objects and configure layout behavior through `Dali::Ui::Layout` subclasses and `Dali::Ui::LayoutParams` subclasses.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ConfigureContainerLayouts()
{
  Dali::Ui::FlexLayout flow = Dali::Ui::FlexLayout::New()
    .SetDirection(Dali::Ui::FlexDirection::ROW)
    .SetWrap(Dali::Ui::FlexWrap::WRAP)
    .SetJustifyContent(Dali::Ui::FlexJustify::FLEX_START)
    .SetAlignItems(Dali::Ui::FlexAlign::STRETCH);

  Dali::Ui::AbsoluteLayout overlay = Dali::Ui::AbsoluteLayout::New();

  Dali::Ui::GridLayout grid = Dali::Ui::GridLayout::New();
  grid.AddRowDefinition(Dali::Ui::GridLength::Absolute(80.0f));
  grid.AddColumnDefinition(Dali::Ui::GridLength::Star(1.0f));
}
```

## Flex Layouts for Flowing Rows and Columns

`Dali::Ui::FlexLayout` arranges child views using the CSS Flexbox algorithm, with direction, wrapping, main-axis distribution, and cross-axis alignment.

Set the direction with `Dali::Ui::FlexLayout::SetDirection`. Use `Dali::Ui::FlexDirection::ROW` or `Dali::Ui::FlexDirection::COLUMN` for normal flow, and `Dali::Ui::FlexDirection::ROW_REVERSE` or `Dali::Ui::FlexDirection::COLUMN_REVERSE` when the order should be reversed on the main axis.

Use `Dali::Ui::FlexLayout::SetWrap` with `Dali::Ui::FlexWrap::NO_WRAP`, `Dali::Ui::FlexWrap::WRAP`, or `Dali::Ui::FlexWrap::WRAP_REVERSE`. Main-axis distribution is controlled by `Dali::Ui::FlexLayout::SetJustifyContent`, and cross-axis defaults are controlled by `Dali::Ui::FlexLayout::SetAlignItems`.

```cpp
Dali::Ui::FlexLayout toolbarLayout = Dali::Ui::FlexLayout::New()
  .SetDirection(Dali::Ui::FlexDirection::ROW)
  .SetWrap(Dali::Ui::FlexWrap::NO_WRAP)
  .SetJustifyContent(Dali::Ui::FlexJustify::SPACE_BETWEEN)
  .SetAlignItems(Dali::Ui::FlexAlign::CENTER);

Dali::Ui::FlexLayout wrappedPanelLayout = Dali::Ui::FlexLayout::New()
  .SetDirection(Dali::Ui::FlexDirection::ROW)
  .SetWrap(Dali::Ui::FlexWrap::WRAP)
  .SetJustifyContent(Dali::Ui::FlexJustify::FLEX_START)
  .SetAlignContent(Dali::Ui::FlexAlign::CENTER);
```

Use `Dali::Ui::FlexLayoutParams` for child-specific flex behavior and attach it to a child with `Dali::Ui::View::SetLayoutParams`. `Dali::Ui::FlexLayoutParams::SetFlexBasis` sets the initial main-axis size before grow or shrink is applied, `Dali::Ui::FlexLayoutParams::SetFlexGrow` lets a child receive remaining space, and `Dali::Ui::FlexLayoutParams::SetFlexShrink` controls how it shrinks when space is tight. `Dali::Ui::FlexLayoutParams::SetAlignSelf` overrides the parent cross-axis alignment for one child.

```cpp
Dali::Ui::FlexLayoutParams primaryItemParams = Dali::Ui::FlexLayoutParams::New()
  .SetFlexBasis(120.0f)
  .SetFlexGrow(1.0f)
  .SetFlexShrink(1.0f)
  .SetAlignSelf(Dali::Ui::FlexAlign::STRETCH);

Dali::Ui::FlexLayoutParams fixedActionParams = Dali::Ui::FlexLayoutParams::New()
  .SetFlexBasis(64.0f)
  .SetFlexGrow(0.0f)
  .SetFlexShrink(0.0f)
  .SetAlignSelf(Dali::Ui::FlexAlign::CENTER);
```

## Absolute Layouts for Explicit Placement

`Dali::Ui::AbsoluteLayout` positions children from per-child `Dali::Ui::AbsoluteLayoutParams`. The child bounds are stored as a `Dali::Ui::LayoutRect` containing `x`, `y`, `width`, and `height`.

Use `Dali::Ui::AbsoluteLayoutParams::SetBounds` when all four values are known together. Use `Dali::Ui::AbsoluteLayoutParams::SetX`, `Dali::Ui::AbsoluteLayoutParams::SetY`, `Dali::Ui::AbsoluteLayoutParams::SetWidth`, and `Dali::Ui::AbsoluteLayoutParams::SetHeight` when updating one axis or dimension independently. Attach the params to the child with `Dali::Ui::View::SetLayoutParams`.

```cpp
Dali::Ui::AbsoluteLayout overlayLayout = Dali::Ui::AbsoluteLayout::New();

Dali::Ui::AbsoluteLayoutParams badgeParams =
  Dali::Ui::AbsoluteLayoutParams::New()
    .SetBounds(Dali::Ui::LayoutRect(24.0f, 16.0f, 48.0f, 48.0f));

Dali::Ui::AbsoluteLayoutParams panelParams =
  Dali::Ui::AbsoluteLayoutParams::New()
    .SetX(96.0f)
    .SetY(72.0f)
    .SetWidth(320.0f)
    .SetHeight(180.0f);
```

`Dali::Ui::AbsoluteLayoutFlags` changes whether each position or size component is interpreted as an absolute value or as a proportion of the parent content area. Combine flags with the public bitwise operators when only some axes should be proportional.

```cpp
Dali::Ui::AbsoluteLayoutParams centeredHalfSizeParams =
  Dali::Ui::AbsoluteLayoutParams::New()
    .SetBounds(Dali::Ui::LayoutRect(0.25f, 0.25f, 0.5f, 0.5f))
    .SetFlags(Dali::Ui::AbsoluteLayoutFlags::POSITION_PROPORTIONAL |
              Dali::Ui::AbsoluteLayoutFlags::SIZE_PROPORTIONAL);

Dali::Ui::AbsoluteLayoutParams proportionalWidthParams =
  Dali::Ui::AbsoluteLayoutParams::New()
    .SetBounds(Dali::Ui::LayoutRect(20.0f, 40.0f, 0.6f, 72.0f))
    .SetFlags(Dali::Ui::AbsoluteLayoutFlags::WIDTH_PROPORTIONAL);
```

## Grid Layouts for Track-Based Sizing

`Dali::Ui::GridLayout` arranges children in row and column tracks. Define tracks with `Dali::Ui::GridLayout::AddRowDefinition` and `Dali::Ui::GridLayout::AddColumnDefinition`.

Use `Dali::Ui::GridLength::Absolute` for fixed pixel tracks, `Dali::Ui::GridLength::Star` for proportional tracks, and `Dali::Ui::GridLength::Auto` for content-driven tracks.

```cpp
Dali::Ui::GridLayout formLayout = Dali::Ui::GridLayout::New();

formLayout.AddColumnDefinition(Dali::Ui::GridLength::Absolute(120.0f));
formLayout.AddColumnDefinition(Dali::Ui::GridLength::Star(1.0f));

formLayout.AddRowDefinition(Dali::Ui::GridLength::Auto());
formLayout.AddRowDefinition(Dali::Ui::GridLength::Auto());
formLayout.AddRowDefinition(Dali::Ui::GridLength::Absolute(56.0f));
```

Use `Dali::Ui::GridLayoutParams` to place a child in a zero-based row and column, optionally spanning multiple tracks or changing cell alignment.

```cpp
Dali::Ui::View field = Dali::Ui::View::New();
field.SetLayoutParams(Dali::Ui::GridLayoutParams::New()
  .SetRow(1u)
  .SetColumn(1u)
  .SetColumnSpan(2u)
  .SetHorizontalAlignment(Dali::Ui::LayoutAlignment::FILL)
  .SetVerticalAlignment(Dali::Ui::LayoutAlignment::CENTER));

formLayout.Add(field);
```

A grid can be rebuilt by clearing the current definitions and adding a new track set. `Dali::Ui::GridLayout::ClearRowDefinitions` removes all row definitions, and `Dali::Ui::GridLayout::ClearColumnDefinitions` removes all column definitions.

```cpp
void ConfigureCompactGrid(Dali::Ui::GridLayout grid)
{
  grid.ClearRowDefinitions();
  grid.ClearColumnDefinitions();

  grid.AddColumnDefinition(Dali::Ui::GridLength::Star(1.0f));

  grid.AddRowDefinition(Dali::Ui::GridLength::Auto());
  grid.AddRowDefinition(Dali::Ui::GridLength::Auto());
  grid.AddRowDefinition(Dali::Ui::GridLength::Auto());
}
```

## Reading Layout State

Typed getters let application code inspect layout configuration without using property IDs or raw property APIs.

For flex containers, use `Dali::Ui::FlexLayout::GetDirection`, `Dali::Ui::FlexLayout::GetWrap`, `Dali::Ui::FlexLayout::GetJustifyContent`, `Dali::Ui::FlexLayout::GetAlignItems`, and `Dali::Ui::FlexLayout::GetAlignContent`.

```cpp
bool IsHorizontalWrappedFlex(Dali::Ui::FlexLayout layout)
{
  return layout.GetDirection() == Dali::Ui::FlexDirection::ROW &&
         layout.GetWrap() == Dali::Ui::FlexWrap::WRAP;
}
```

For absolute layout parameters, use `Dali::Ui::AbsoluteLayoutParams::GetBounds`, `Dali::Ui::AbsoluteLayoutParams::GetX`, `Dali::Ui::AbsoluteLayoutParams::GetY`, `Dali::Ui::AbsoluteLayoutParams::GetWidth`, `Dali::Ui::AbsoluteLayoutParams::GetHeight`, and `Dali::Ui::AbsoluteLayoutParams::GetFlags`.

```cpp
bool UsesProportionalSize(Dali::Ui::AbsoluteLayoutParams params)
{
  Dali::Ui::AbsoluteLayoutFlags flags = params.GetFlags();

  return (flags & Dali::Ui::AbsoluteLayoutFlags::SIZE_PROPORTIONAL) ==
         Dali::Ui::AbsoluteLayoutFlags::SIZE_PROPORTIONAL;
}
```

For grids, use `Dali::Ui::GridLayout::GetRowCount`, `Dali::Ui::GridLayout::GetColumnCount`, `Dali::Ui::GridLayout::GetRowDefinitions`, `Dali::Ui::GridLayout::GetColumnDefinitions`, `Dali::Ui::GridLayout::GetRowSpacing`, and `Dali::Ui::GridLayout::GetColumnSpacing`.

```cpp
bool HasUsableGridTracks(Dali::Ui::GridLayout grid)
{
  return grid.GetRowCount() > 0u && grid.GetColumnCount() > 0u;
}
```

## Public Layout Handles and Integration Types

Application developers normally use the public layout and layout parameter types: `Dali::Ui::Layout`, `Dali::Ui::LayoutParams`, `Dali::Ui::AbsoluteLayout`, `Dali::Ui::AbsoluteLayoutParams`, `Dali::Ui::FlexLayout`, `Dali::Ui::FlexLayoutParams`, `Dali::Ui::GridLayout`, `Dali::Ui::GridLayoutParams`, `Dali::Ui::StackLayout`, and `Dali::Ui::StackLayoutParams`.

The `Dali::Ui::Integration` layout implementation and manager classes are part of the layout integration layer. They include integration-header types such as `Dali::Ui::Integration::LayoutImpl`, `Dali::Ui::Integration::AbsoluteLayoutImpl`, `Dali::Ui::Integration::AbsoluteLayoutManager`, `Dali::Ui::Integration::FlexLayoutImpl`, `Dali::Ui::Integration::FlexLayoutManager`, `Dali::Ui::Integration::GridLayoutImpl`, `Dali::Ui::Integration::GridLayoutManager`, `Dali::Ui::Integration::StackLayoutImpl`, `Dali::Ui::Integration::StackLayoutManager`, and `Dali::Ui::Integration::ScrollViewLayoutManager`. `Dali::Ui::LayoutController` is a public controller for scheduling layout processing around a `Dali::Window`; its internal implementation is not an app-facing layout object.

Keep app code on the public `Dali::Ui` layout and layout parameter types unless you are writing framework integration code.

```cpp
Dali::Ui::Layout appLayout = Dali::Ui::FlexLayout::New()
  .SetDirection(Dali::Ui::FlexDirection::COLUMN)
  .SetWrap(Dali::Ui::FlexWrap::NO_WRAP)
  .SetAlignItems(Dali::Ui::FlexAlign::STRETCH);

Dali::Ui::LayoutParams childParams = Dali::Ui::FlexLayoutParams::New()
  .SetFlexGrow(1.0f)
  .SetFlexShrink(1.0f);
```
