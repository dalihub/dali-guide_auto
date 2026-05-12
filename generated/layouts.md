---
title: Layouts
sidebar_label: Layouts
category: layout-sizing
---

# Layouts

`dali-ui` layouts arrange `Dali::Ui::View` children by using typed layout objects and typed per-child layout parameters.

## Table of Contents

- [Choosing a Layout](#choosing-a-layout)
- [Flex Rows and Columns](#flex-rows-and-columns)
- [Grid Rows and Columns](#grid-rows-and-columns)
- [Absolute Bounds](#absolute-bounds)
- [Reading and Reusing Layout Configuration](#reading-and-reusing-layout-configuration)

## Choosing a Layout

Use a `Dali::Ui::Layout` subclass as the container view when the container owns the positioning rule for its children. In a dali-ui application, the common choices are `Dali::Ui::FlexLayout`, `Dali::Ui::GridLayout`, and `Dali::Ui::AbsoluteLayout`.

- Use `Dali::Ui::FlexLayout` when children should flow in a row or column and optionally grow, shrink, wrap, or align.
- Use `Dali::Ui::GridLayout` when the available area should be divided into rows and columns.
- Use `Dali::Ui::AbsoluteLayout` when each child has explicit bounds inside the parent.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::FlexLayout MakeToolbarLayout()
{
  return Dali::Ui::FlexLayout::New()
    .SetDirection(Dali::Ui::FlexDirection::ROW)
    .SetWrap(Dali::Ui::FlexWrap::NO_WRAP)
    .SetJustifyContent(Dali::Ui::FlexJustify::SPACE_BETWEEN)
    .SetAlignItems(Dali::Ui::FlexAlign::CENTER);
}

Dali::Ui::GridLayout MakeDashboardGrid()
{
  Dali::Ui::GridLayout grid = Dali::Ui::GridLayout::New();
  grid.AddColumnDefinition(Dali::Ui::GridLength::Star(1.0f));
  grid.AddColumnDefinition(Dali::Ui::GridLength::Star(2.0f));
  grid.AddRowDefinition(Dali::Ui::GridLength::Auto());
  grid.AddRowDefinition(Dali::Ui::GridLength::Star(1.0f));
  return grid;
}

Dali::Ui::AbsoluteLayout MakeOverlayLayer()
{
  return Dali::Ui::AbsoluteLayout::New();
}
```

## Flex Rows and Columns

`Dali::Ui::FlexLayout` arranges child `Dali::Ui::View` objects with a flexbox-style algorithm. Configure the container with `Dali::Ui::FlexLayout::SetDirection`, `Dali::Ui::FlexLayout::SetWrap`, `Dali::Ui::FlexLayout::SetJustifyContent`, `Dali::Ui::FlexLayout::SetAlignItems`, and `Dali::Ui::FlexLayout::SetAlignContent`.

Use `Dali::Ui::FlexLayoutParams` on each child when an individual view needs its own grow, shrink, basis, or cross-axis alignment rule.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::FlexLayout CreateWrappingActionRow(Dali::Ui::View primaryAction,
                                             Dali::Ui::View secondaryAction,
                                             Dali::Ui::View trailingAction)
{
  Dali::Ui::FlexLayout row = Dali::Ui::FlexLayout::New()
    .SetDirection(Dali::Ui::FlexDirection::ROW)
    .SetWrap(Dali::Ui::FlexWrap::WRAP)
    .SetJustifyContent(Dali::Ui::FlexJustify::FLEX_START)
    .SetAlignItems(Dali::Ui::FlexAlign::CENTER)
    .SetAlignContent(Dali::Ui::FlexAlign::CENTER);

  primaryAction.SetLayoutParams(
    Dali::Ui::FlexLayoutParams::New()
      .SetFlexBasis(160.0f)
      .SetFlexGrow(1.0f)
      .SetFlexShrink(1.0f)
      .SetAlignSelf(Dali::Ui::FlexAlign::CENTER));

  secondaryAction.SetLayoutParams(
    Dali::Ui::FlexLayoutParams::New()
      .SetFlexBasis(120.0f)
      .SetFlexGrow(0.0f)
      .SetFlexShrink(1.0f));

  trailingAction.SetLayoutParams(
    Dali::Ui::FlexLayoutParams::New()
      .SetFlexBasis(96.0f)
      .SetFlexGrow(0.0f)
      .SetFlexShrink(0.0f)
      .SetAlignSelf(Dali::Ui::FlexAlign::FLEX_END));

  row.Add(primaryAction);
  row.Add(secondaryAction);
  row.Add(trailingAction);

  return row;
}
```

For vertical content, change only the main axis with `Dali::Ui::FlexDirection::COLUMN`. The same child parameters still apply; `Dali::Ui::FlexLayoutParams::SetFlexBasis` then refers to the initial size on the vertical main axis.

```cpp
Dali::Ui::FlexLayout CreateSidePanel(Dali::Ui::View header,
                                     Dali::Ui::View body,
                                     Dali::Ui::View footer)
{
  Dali::Ui::FlexLayout column = Dali::Ui::FlexLayout::New()
    .SetDirection(Dali::Ui::FlexDirection::COLUMN)
    .SetWrap(Dali::Ui::FlexWrap::NO_WRAP)
    .SetJustifyContent(Dali::Ui::FlexJustify::FLEX_START)
    .SetAlignItems(Dali::Ui::FlexAlign::STRETCH);

  header.SetLayoutParams(Dali::Ui::FlexLayoutParams::New().SetFlexShrink(0.0f));
  body.SetLayoutParams(Dali::Ui::FlexLayoutParams::New().SetFlexGrow(1.0f).SetFlexShrink(1.0f));
  footer.SetLayoutParams(Dali::Ui::FlexLayoutParams::New().SetFlexShrink(0.0f));

  column.Add(header);
  column.Add(body);
  column.Add(footer);

  return column;
}
```

## Grid Rows and Columns

`Dali::Ui::GridLayout` defines rows and columns with `Dali::Ui::GridLength`. Use `Dali::Ui::GridLength::Absolute` for fixed pixel tracks, `Dali::Ui::GridLength::Star` for proportional tracks, and `Dali::Ui::GridLength::Auto` for content-driven tracks.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::GridLayout CreateTwoColumnFormGrid()
{
  Dali::Ui::GridLayout grid = Dali::Ui::GridLayout::New();

  grid.AddColumnDefinition(Dali::Ui::GridLength::Absolute(140.0f));
  grid.AddColumnDefinition(Dali::Ui::GridLength::Star(1.0f));

  grid.AddRowDefinition(Dali::Ui::GridLength::Auto());
  grid.AddRowDefinition(Dali::Ui::GridLength::Auto());
  grid.AddRowDefinition(Dali::Ui::GridLength::Auto());

  return grid;
}
```

Attach `Dali::Ui::GridLayoutParams` to child views to choose the row, column, and span for each child. Row and column indices are zero-based.

```cpp
Dali::Ui::GridLayout CreateFormSection(Dali::Ui::View title,
                                       Dali::Ui::View nameLabel,
                                       Dali::Ui::View nameField,
                                       Dali::Ui::View emailLabel,
                                       Dali::Ui::View emailField)
{
  Dali::Ui::GridLayout grid = CreateTwoColumnFormGrid();

  title.SetLayoutParams(
    Dali::Ui::GridLayoutParams::New()
      .SetRow(0)
      .SetColumn(0)
      .SetColumnSpan(2));

  nameLabel.SetLayoutParams(
    Dali::Ui::GridLayoutParams::New()
      .SetRow(1)
      .SetColumn(0));

  nameField.SetLayoutParams(
    Dali::Ui::GridLayoutParams::New()
      .SetRow(1)
      .SetColumn(1));

  emailLabel.SetLayoutParams(
    Dali::Ui::GridLayoutParams::New()
      .SetRow(2)
      .SetColumn(0));

  emailField.SetLayoutParams(
    Dali::Ui::GridLayoutParams::New()
      .SetRow(2)
      .SetColumn(1));

  grid.Add(title);
  grid.Add(nameLabel);
  grid.Add(nameField);
  grid.Add(emailLabel);
  grid.Add(emailField);

  return grid;
}
```

When rebuilding a grid at runtime, use `Dali::Ui::GridLayout::ClearRowDefinitions` and `Dali::Ui::GridLayout::ClearColumnDefinitions` before adding the replacement definitions.

```cpp
void UseCompactGrid(Dali::Ui::GridLayout grid)
{
  grid.ClearColumnDefinitions();
  grid.ClearRowDefinitions();

  grid.AddColumnDefinition(Dali::Ui::GridLength::Star(1.0f));
  grid.AddRowDefinition(Dali::Ui::GridLength::Auto());
  grid.AddRowDefinition(Dali::Ui::GridLength::Auto());
  grid.AddRowDefinition(Dali::Ui::GridLength::Auto());
}
```

## Absolute Bounds

`Dali::Ui::AbsoluteLayout` positions child views from explicit `Dali::Ui::LayoutRect` bounds. `Dali::Ui::AbsoluteLayoutParams::SetBounds` sets the child x, y, width, and height values.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::AbsoluteLayout CreateBadgeLayer(Dali::Ui::View content,
                                          Dali::Ui::View badge)
{
  Dali::Ui::AbsoluteLayout layer = Dali::Ui::AbsoluteLayout::New();

  content.SetLayoutParams(
    Dali::Ui::AbsoluteLayoutParams::New()
      .SetBounds(Dali::Ui::LayoutRect(0.0f, 0.0f, 1.0f, 1.0f))
      .SetFlags(Dali::Ui::AbsoluteLayoutFlags::SIZE_PROPORTIONAL));

  badge.SetLayoutParams(
    Dali::Ui::AbsoluteLayoutParams::New()
      .SetBounds(Dali::Ui::LayoutRect(0.85f, 0.0f, 48.0f, 48.0f))
      .SetFlags(Dali::Ui::AbsoluteLayoutFlags::POSITION_PROPORTIONAL));

  layer.Add(content);
  layer.Add(badge);

  return layer;
}
```

Use `Dali::Ui::AbsoluteLayoutFlags` to decide which axes are proportional to the parent content area. `Dali::Ui::AbsoluteLayoutFlags::SIZE_PROPORTIONAL` makes width and height proportional, `Dali::Ui::AbsoluteLayoutFlags::POSITION_PROPORTIONAL` makes x and y proportional, and `Dali::Ui::AbsoluteLayoutFlags::ALL` applies proportional interpretation to position and size.

```cpp
Dali::Ui::AbsoluteLayoutParams CreateCenteredHalfSizeBounds()
{
  return Dali::Ui::AbsoluteLayoutParams::New()
    .SetX(0.25f)
    .SetY(0.25f)
    .SetWidth(0.5f)
    .SetHeight(0.5f)
    .SetFlags(Dali::Ui::AbsoluteLayoutFlags::ALL);
}

Dali::Ui::AbsoluteLayoutParams CreateFixedSizeAtProportionalPosition()
{
  return Dali::Ui::AbsoluteLayoutParams::New()
    .SetBounds(Dali::Ui::LayoutRect(0.5f, 0.5f, 120.0f, 80.0f))
    .SetFlags(Dali::Ui::AbsoluteLayoutFlags::POSITION_PROPORTIONAL);
}
```

## Reading and Reusing Layout Configuration

The layout handles and params handles expose typed getters. This lets application code inspect a current layout rule without falling back to generic property access.

```cpp
bool IsWrappingRow(Dali::Ui::FlexLayout layout)
{
  return layout.GetDirection() == Dali::Ui::FlexDirection::ROW &&
         layout.GetWrap() == Dali::Ui::FlexWrap::WRAP;
}

Dali::Ui::FlexLayoutParams CopyAsNonShrinking(Dali::Ui::FlexLayoutParams source)
{
  return Dali::Ui::FlexLayoutParams::New(source)
    .SetFlexShrink(0.0f);
}
```

`Dali::Ui::GridLayout` can report its current row and column definitions and counts, which is useful when a view mode needs to preserve an existing grid shape.

```cpp
uint32_t GetGridCellCount(Dali::Ui::GridLayout grid)
{
  return grid.GetRowCount() * grid.GetColumnCount();
}

void MirrorGridTracks(Dali::Ui::GridLayout source, Dali::Ui::GridLayout target)
{
  Dali::Vector<Dali::Ui::GridLength> rows = source.GetRowDefinitions();
  Dali::Vector<Dali::Ui::GridLength> columns = source.GetColumnDefinitions();

  target.ClearRowDefinitions();
  target.ClearColumnDefinitions();

  for(uint32_t index = 0u; index < rows.Count(); ++index)
  {
    target.AddRowDefinition(rows[index]);
  }

  for(uint32_t index = 0u; index < columns.Count(); ++index)
  {
    target.AddColumnDefinition(columns[index]);
  }
}
```

For absolute children, read back `Dali::Ui::AbsoluteLayoutParams` when application code needs to preserve the child position while changing one dimension.

```cpp
Dali::Ui::AbsoluteLayoutParams WithNewHeight(Dali::Ui::AbsoluteLayoutParams source,
                                             float height)
{
  Dali::Ui::LayoutRect bounds = source.GetBounds();

  return Dali::Ui::AbsoluteLayoutParams::New(source)
    .SetBounds(Dali::Ui::LayoutRect(bounds.x, bounds.y, bounds.width, height))
    .SetFlags(source.GetFlags());
}
```
