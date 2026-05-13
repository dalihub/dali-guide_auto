---
title: Layouts
sidebar_label: Layouts
category: layout-sizing
---

# Layouts

Layouts arrange `Dali::Ui::View` children with typed dali-ui layout objects and per-child `LayoutParams`.

## Table of Contents

- [Build Layout Trees with `Dali::Ui::View`](#build-layout-trees-with-daliuiview)
- [Absolute Placement with `Dali::Ui::AbsoluteLayout`](#absolute-placement-with-daliuiabsolutelayout)
- [Flexible Rows and Columns with `Dali::Ui::FlexLayout`](#flexible-rows-and-columns-with-daliuiflexlayout)
- [Row and Column Sizing with `Dali::Ui::GridLayout`](#row-and-column-sizing-with-daliuigridlayout)

## Build Layout Trees with `Dali::Ui::View`

`Dali::Ui::Layout` is a `Dali::Ui::View` specialization that owns a layout manager. Application code usually creates a concrete layout such as `Dali::Ui::AbsoluteLayout`, `Dali::Ui::FlexLayout`, or `Dali::Ui::GridLayout`, then attaches child-specific `Dali::Ui::LayoutParams` to each child `Dali::Ui::View`.

Use `Dali::Ui::View::SetLayoutParams()` to attach parameters. Use the parameter type for the parent layout: `Dali::Ui::AbsoluteLayoutParams` for `Dali::Ui::AbsoluteLayout`, `Dali::Ui::FlexLayoutParams` for `Dali::Ui::FlexLayout`, and `Dali::Ui::GridLayoutParams` for `Dali::Ui::GridLayout`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

FlexLayout row = FlexLayout::New()
  .SetDirection(FlexDirection::ROW)
  .SetJustifyContent(FlexJustify::CENTER)
  .SetAlignItems(FlexAlign::CENTER);

View primary = View::New();
primary.SetLayoutParams(
  FlexLayoutParams::New()
    .SetFlexGrow(1.0f)
    .SetFlexShrink(1.0f));

View secondary = View::New();
secondary.SetLayoutParams(
  FlexLayoutParams::New()
    .SetFlexBasis(120.0f)
    .SetFlexShrink(0.0f));

row.Children({primary, secondary});
```

For shared code that receives a generic handle, `Dali::Ui::LayoutParams::DownCast()` can identify a layout-params handle before using a more specific downcast such as `Dali::Ui::FlexLayoutParams::DownCast()`.

```cpp
void InspectFlexParams(BaseHandle handle)
{
  LayoutParams params = LayoutParams::DownCast(handle);
  FlexLayoutParams flexParams = FlexLayoutParams::DownCast(params);

  if(flexParams)
  {
    const float grow = flexParams.GetFlexGrow();
    const float basis = flexParams.GetFlexBasis();
    (void)grow;
    (void)basis;
  }
}
```

## Absolute Placement with `Dali::Ui::AbsoluteLayout`

`Dali::Ui::AbsoluteLayout` positions children from explicit bounds. Use `Dali::Ui::AbsoluteLayoutParams::SetBounds()` when the child has a complete rectangle, or combine `SetX()`, `SetY()`, `SetWidth()`, and `SetHeight()` when you want to update individual axes.

A width or height of `-1` in `Dali::Ui::AbsoluteLayoutParams` means that axis uses the child view's own size. Non-negative bounds dimensions set the explicit size for that axis.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

AbsoluteLayout canvas = AbsoluteLayout::New();

View panel = View::New();
panel.SetLayoutParams(
  AbsoluteLayoutParams::New()
    .SetBounds(LayoutRect(24.0f, 32.0f, 280.0f, 160.0f)));

View badge = View::New();
badge.SetLayoutParams(
  AbsoluteLayoutParams::New()
    .SetX(256.0f)
    .SetY(16.0f)
    .SetWidth(48.0f)
    .SetHeight(48.0f));

canvas.Children({panel, badge});
```

Use `Dali::Ui::AbsoluteLayoutFlags` when positions or sizes should be interpreted proportionally to the parent content area. `Dali::Ui::AbsoluteLayoutFlags::POSITION_PROPORTIONAL` makes `x` and `y` proportional, and `Dali::Ui::AbsoluteLayoutFlags::SIZE_PROPORTIONAL` makes `width` and `height` proportional.

```cpp
AbsoluteLayout overlay = AbsoluteLayout::New();

View centeredRegion = View::New();
centeredRegion.SetLayoutParams(
  AbsoluteLayoutParams::New()
    .SetBounds(LayoutRect(0.25f, 0.25f, 0.5f, 0.5f))
    .SetFlags(AbsoluteLayoutFlags::POSITION_PROPORTIONAL |
              AbsoluteLayoutFlags::SIZE_PROPORTIONAL));

overlay.Children({centeredRegion});
```

You can copy an existing `Dali::Ui::AbsoluteLayoutParams` object with `Dali::Ui::AbsoluteLayoutParams::New(const AbsoluteLayoutParams&)`, then adjust only the fields that differ.

```cpp
AbsoluteLayoutParams baseParams = AbsoluteLayoutParams::New()
  .SetWidth(96.0f)
  .SetHeight(96.0f);

View first = View::New();
first.SetLayoutParams(
  AbsoluteLayoutParams::New(baseParams)
    .SetX(16.0f)
    .SetY(16.0f));

View second = View::New();
second.SetLayoutParams(
  AbsoluteLayoutParams::New(baseParams)
    .SetX(128.0f)
    .SetY(16.0f));
```

## Flexible Rows and Columns with `Dali::Ui::FlexLayout`

`Dali::Ui::FlexLayout` arranges children using flexbox-style row or column rules. Configure the container with `SetDirection()`, `SetWrap()`, `SetJustifyContent()`, `SetAlignItems()`, and `SetAlignContent()`.

Use `Dali::Ui::FlexLayoutParams` on children to control flexible sizing. `SetFlexBasis()` sets the initial main-axis size, `SetFlexGrow()` distributes remaining space, `SetFlexShrink()` controls shrink behavior, and `SetAlignSelf()` overrides the parent cross-axis alignment for one child.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

FlexLayout toolbar = FlexLayout::New()
  .SetDirection(FlexDirection::ROW)
  .SetWrap(FlexWrap::NO_WRAP)
  .SetJustifyContent(FlexJustify::SPACE_BETWEEN)
  .SetAlignItems(FlexAlign::CENTER);

View title = View::New();
title.SetLayoutParams(
  FlexLayoutParams::New()
    .SetFlexGrow(1.0f)
    .SetFlexShrink(1.0f)
    .SetFlexBasis(160.0f));

View action = View::New();
action.SetLayoutParams(
  FlexLayoutParams::New()
    .SetFlexGrow(0.0f)
    .SetFlexShrink(0.0f)
    .SetFlexBasis(96.0f)
    .SetAlignSelf(FlexAlign::CENTER));

toolbar.Children({title, action});
```

For wrapped content, `Dali::Ui::FlexLayout::SetAlignContent()` controls how flex lines are aligned in the cross axis. This is separate from `Dali::Ui::FlexLayout::SetAlignItems()`, which aligns items within each line.

```cpp
FlexLayout chips = FlexLayout::New()
  .SetDirection(FlexDirection::ROW)
  .SetWrap(FlexWrap::WRAP)
  .SetJustifyContent(FlexJustify::FLEX_START)
  .SetAlignItems(FlexAlign::CENTER)
  .SetAlignContent(FlexAlign::FLEX_START);

View chipA = View::New();
chipA.SetLayoutParams(FlexLayoutParams::New().SetFlexBasis(96.0f));

View chipB = View::New();
chipB.SetLayoutParams(FlexLayoutParams::New().SetFlexBasis(128.0f));

View chipC = View::New();
chipC.SetLayoutParams(FlexLayoutParams::New().SetFlexBasis(112.0f));

chips.Children({chipA, chipB, chipC});
```

The getter methods mirror the setter methods, which is useful when a component needs to preserve or copy layout policy.

```cpp
FlexLayout source = FlexLayout::New()
  .SetDirection(FlexDirection::COLUMN)
  .SetWrap(FlexWrap::NO_WRAP)
  .SetJustifyContent(FlexJustify::CENTER)
  .SetAlignItems(FlexAlign::STRETCH);

FlexLayout copy = FlexLayout::New()
  .SetDirection(source.GetDirection())
  .SetWrap(source.GetWrap())
  .SetJustifyContent(source.GetJustifyContent())
  .SetAlignItems(source.GetAlignItems());
```

## Row and Column Sizing with `Dali::Ui::GridLayout`

`Dali::Ui::GridLayout` arranges children in rows and columns. Define the grid with `Dali::Ui::GridLayout::AddRowDefinition()` and `Dali::Ui::GridLayout::AddColumnDefinition()`. Each definition uses `Dali::Ui::GridLength`: `GridLength::Absolute()` for fixed pixels, `GridLength::Star()` for proportional remaining space, and `GridLength::Auto()` for content-driven sizing.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

GridLayout form = GridLayout::New()
  .SetRowSpacing(12.0f)
  .SetColumnSpacing(16.0f);

form.AddColumnDefinition(GridLength::Absolute(120.0f));
form.AddColumnDefinition(GridLength::Star(1.0f));
form.AddRowDefinition(GridLength::Auto());
form.AddRowDefinition(GridLength::Auto());

View label = View::New();
label.SetLayoutParams(
  GridLayoutParams::New()
    .SetRow(0u)
    .SetColumn(0u));

View field = View::New();
field.SetLayoutParams(
  GridLayoutParams::New()
    .SetRow(0u)
    .SetColumn(1u)
    .SetHorizontalAlignment(LayoutAlignment::FILL)
    .SetVerticalAlignment(LayoutAlignment::CENTER));

form.Children({label, field});
```

Use spans when one view should occupy multiple cells. `Dali::Ui::GridLayoutParams::SetRowSpan()` and `Dali::Ui::GridLayoutParams::SetColumnSpan()` take counts, while `SetRow()` and `SetColumn()` take zero-based starting indexes.

```cpp
GridLayout dashboard = GridLayout::New()
  .SetRowSpacing(8.0f)
  .SetColumnSpacing(8.0f);

dashboard.AddColumnDefinition(GridLength::Star(1.0f));
dashboard.AddColumnDefinition(GridLength::Star(1.0f));
dashboard.AddColumnDefinition(GridLength::Star(1.0f));
dashboard.AddRowDefinition(GridLength::Absolute(80.0f));
dashboard.AddRowDefinition(GridLength::Star(1.0f));

View header = View::New();
header.SetLayoutParams(
  GridLayoutParams::New()
    .SetRow(0u)
    .SetColumn(0u)
    .SetColumnSpan(3u)
    .SetHorizontalAlignment(LayoutAlignment::FILL)
    .SetVerticalAlignment(LayoutAlignment::FILL));

View content = View::New();
content.SetLayoutParams(
  GridLayoutParams::New()
    .SetRow(1u)
    .SetColumn(0u)
    .SetColumnSpan(2u));

dashboard.Children({header, content});
```

When a grid needs to be rebuilt, clear its definitions before adding the new row or column model. `GetRowCount()`, `GetColumnCount()`, `GetRowSpacing()`, and `GetColumnSpacing()` let you inspect the current layout configuration.

```cpp
void ConfigureCompactGrid(GridLayout grid)
{
  grid.ClearRowDefinitions();
  grid.ClearColumnDefinitions();

  grid.AddColumnDefinition(GridLength::Star(1.0f));
  grid.AddRowDefinition(GridLength::Auto());
  grid.AddRowDefinition(GridLength::Auto());

  const uint32_t rows = grid.GetRowCount();
  const uint32_t columns = grid.GetColumnCount();
  const float rowGap = grid.GetRowSpacing();
  const float columnGap = grid.GetColumnSpacing();

  (void)rows;
  (void)columns;
  (void)rowGap;
  (void)columnGap;
}
```
