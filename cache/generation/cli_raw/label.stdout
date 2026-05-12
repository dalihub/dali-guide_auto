---
title: Label
sidebar_label: Label
category: views-components
---

# Label

`Dali::Ui::Label` is the dali-ui `View` used to display non-editable text in an application UI.

## Table of contents

- [Create a Label as a View](#create-a-label-as-a-view)
- [Configure Typography](#configure-typography)
- [Control Text Layout](#control-text-layout)
- [Use Anchors](#use-anchors)
- [Use Asynchronous Rendering](#use-asynchronous-rendering)
- [Use Label Properties](#use-label-properties)

## Create a Label as a View

Create text with `Dali::Ui::Label::New`. In dali-ui application code, keep the object in the `Dali::Ui::View` tree, and use `Dali::Ui::Label` when you need label-specific configuration.

```cpp
#include <dali-ui-foundation/public-api/label.h>
#include <dali-ui-foundation/public-api/view.h>

using namespace Dali;
using namespace Dali::Ui;

View CreateTitleView()
{
  Label title = Label::New("Settings");

  title
    .SetFontFamily("SamsungOneUI_700")
    .SetFontSize(32.0f)
    .SetFontWeight(Text::FontWeight::BOLD);

  View view = title;
  return view;
}
```

`Dali::Ui::Label` derives from `Dali::Ui::View`, so the app-facing object can be returned, stored, or composed as a `Dali::Ui::View` while still being configured through typed label setters.

## Configure Typography

Use typed setters such as `SetFontFamily`, `SetFontSize`, `SetFontSizeScale`, `SetFontWeight`, `SetFontWidth`, and `SetFontSlant` for text styling. The matching getters, including `GetFontFamily`, `GetFontSize`, `GetFontSizeScale`, `GetFontWeight`, `GetFontWidth`, and `GetFontSlant`, report the values currently set on the label.

```cpp
#include <dali-ui-foundation/public-api/label.h>

using namespace Dali;
using namespace Dali::Ui;

Label CreateStatusLabel()
{
  return Label::New("Connected")
    .SetFontFamily("SamsungOneUI_400")
    .SetFontSize(18.0f)
    .SetFontSizeScale(1.0f)
    .SetFontWeight(Text::FontWeight::MEDIUM)
    .SetFontWidth(Text::FontWidth::NORMAL)
    .SetFontSlant(Text::FontSlant::NORMAL);
}

void UpdateForEmphasis(Label label)
{
  label
    .SetFontWeight(Text::FontWeight::BOLD)
    .SetFontSlant(Text::FontSlant::ITALIC);

  const float fontSize = label.GetFontSize();
  label.SetFontSize(fontSize + 2.0f);
}
```

For variable fonts, `SetFontVariation` replaces the label's current font variation axes. `GetFontVariation` returns the configured `Dali::Vector<Text::FontVariationAxis>`.

```cpp
#include <dali-ui-foundation/public-api/label.h>
#include <dali-ui-foundation/public-api/text/font-variation/font-variation-axis.h>

using namespace Dali;
using namespace Dali::Ui;

void ApplyVariation(Label label, const Dali::Vector<Text::FontVariationAxis>& axes)
{
  label.SetFontVariation(axes);

  Dali::Vector<Text::FontVariationAxis> appliedAxes = label.GetFontVariation();
}
```

Use `SetFontSizeScale`, `GetMinimumFontSizeScale`, `GetMaximumFontSizeScale`, and `GetAdjustedFontSizeScale` when the label must participate in text scaling. `GetAdjustedFontSizeScale` is the resolved scale after the label applies minimum and maximum constraints and any enabled system scale behavior.

```cpp
#include <dali-ui-foundation/public-api/label.h>

using namespace Dali::Ui;

float ConfigureReadableScale(Label label)
{
  label.SetFontSize(16.0f)
       .SetFontSizeScale(1.15f);

  return label.GetAdjustedFontSizeScale();
}
```

## Control Text Layout

`Dali::Ui::Label` owns text layout settings such as `SetHorizontalTextAlignment`, `SetLineHeight`, and `SetLayoutDirectionMode`. Horizontal alignment uses `Text::Alignment`; layout direction resolution uses `Text::LayoutDirectionMode`.

```cpp
#include <dali-ui-foundation/public-api/label.h>

using namespace Dali;
using namespace Dali::Ui;

Label CreateCenteredMessage(const Dali::String& message)
{
  return Label::New(message)
    .SetFontSize(20.0f)
    .SetHorizontalTextAlignment(Text::Alignment::CENTER)
    .SetLineHeight(1.4f)
    .SetLayoutDirectionMode(Text::LayoutDirectionMode::CONTENTS);
}
```

`SetLineHeight` stores a line-height value on the label. With the label's line-height mode set to relative, that value is interpreted as a multiplier of the effective scaled font size. With absolute line-height mode, it is interpreted as a pixel value. `GetLineHeight` returns the currently stored value.

```cpp
#include <dali-ui-foundation/public-api/label.h>

using namespace Dali::Ui;

void MatchCompactTextMetrics(Label label)
{
  label.SetFontSize(14.0f)
       .SetLineHeight(1.2f);

  const float currentLineHeight = label.GetLineHeight();
}
```

For diagnostics after layout, use `GetLineCount` to query the line count for the current layout width, or `GetAsyncLineCount` after an asynchronous text result has completed.

```cpp
#include <dali-ui-foundation/public-api/label.h>

using namespace Dali::Ui;

int ReadCurrentLineCount(Label label)
{
  return label.GetLineCount();
}

int ReadLastAsyncLineCount(const Label& label)
{
  return label.GetAsyncLineCount();
}
```

## Use Anchors

A label can style anchor text with `SetAnchorColor` and `SetAnchorClickedColor`. `AnchorClickedSignal` is emitted with the clicked `Dali::Ui::View` and the anchor `href` string.

```cpp
#include <dali-ui-foundation/public-api/label.h>
#include <dali-ui-foundation/public-api/ui-color.h>

using namespace Dali;
using namespace Dali::Ui;

class LinkHandler
{
public:
  Label CreateLinkLabel(const UiColor& linkColor, const UiColor& pressedColor)
  {
    Label label = Label::New("Open documentation");

    label
      .SetAnchorColor(linkColor)
      .SetAnchorClickedColor(pressedColor);

    label.AnchorClickedSignal().Connect(this, &LinkHandler::OnAnchorClicked);
    return label;
  }

private:
  void OnAnchorClicked(View view, const Dali::String& href)
  {
    Label label = Label::DownCast(view);
    if(label)
    {
      const UiColor clickedColor = label.GetAnchorClickedColor();
    }
  }
};
```

Use `GetAnchorColor` and `GetAnchorClickedColor` when synchronizing label state with your app theme or interaction state.

## Use Asynchronous Rendering

`SetAsyncRendering` enables asynchronous text rendering for a label. When enabled, the label requests asynchronous text rendering during relayout as needed. `GetAsyncLineCount` reports the line count from the most recent asynchronous result.

```cpp
#include <dali-ui-foundation/public-api/label.h>

using namespace Dali;
using namespace Dali::Ui;

Label CreateAsyncBodyLabel(const Dali::String& text)
{
  return Label::New(text)
    .SetFontFamily("SamsungOneUI_400")
    .SetFontSize(16.0f)
    .SetLineHeight(1.35f)
    .SetAsyncRendering(true);
}

int ReadAsyncResult(Label label)
{
  return label.GetAsyncLineCount();
}
```

For scaled text rendering quality, use `GetRenderScale` to inspect the current render scale. The label property model also exposes `Dali::Ui::Label::Property::RENDER_SCALE`; it applies to asynchronous rendering and does not change the layout size.

```cpp
#include <dali-ui-foundation/public-api/label.h>

using namespace Dali::Ui;

float ReadRenderScale(Label label)
{
  label.SetAsyncRendering(true);
  return label.GetRenderScale();
}
```

## Use Label Properties

Prefer typed setters such as `SetFontSize`, `SetFontFamily`, `SetLineHeight`, `SetAnchorColor`, and `SetAsyncRendering` in application code. `Dali::Ui::Label::Property` is still the label-owned property namespace for integration points that need property indices.

Common label-owned property groups include:

- Content: `Dali::Ui::Label::Property::TEXT`
- Font: `Dali::Ui::Label::Property::FONT_FAMILY`, `Dali::Ui::Label::Property::FONT_SIZE`, `Dali::Ui::Label::Property::FONT_SIZE_SCALE`, `Dali::Ui::Label::Property::FONT_WEIGHT`, `Dali::Ui::Label::Property::FONT_WIDTH`, `Dali::Ui::Label::Property::FONT_SLANT`
- Layout: `Dali::Ui::Label::Property::HORIZONTAL_ALIGNMENT`, `Dali::Ui::Label::Property::VERTICAL_ALIGNMENT`, `Dali::Ui::Label::Property::LINE_HEIGHT`, `Dali::Ui::Label::Property::LINE_HEIGHT_MODE`, `Dali::Ui::Label::Property::LINE_WRAP_MODE`, `Dali::Ui::Label::Property::LAYOUT_DIRECTION_MODE`
- Color: `Dali::Ui::Label::Property::TEXT_COLOR`, `Dali::Ui::Label::Property::TEXT_BACKGROUND_COLOR`, `Dali::Ui::Label::Property::ANCHOR_COLOR`, `Dali::Ui::Label::Property::ANCHOR_CLICKED_COLOR`
- Rendering: `Dali::Ui::Label::Property::ASYNC_RENDERING`, `Dali::Ui::Label::Property::RENDER_SCALE`, `Dali::Ui::Label::Property::CUTOUT_ENABLED`, `Dali::Ui::Label::Property::PIXEL_SNAP_FACTOR`

```cpp
#include <dali-ui-foundation/public-api/label.h>

using namespace Dali::Ui;

int FontSizePropertyIndex()
{
  return Label::Property::FONT_SIZE;
}

int AsyncRenderingPropertyIndex()
{
  return Label::Property::ASYNC_RENDERING;
}
```

For normal app code, the same configuration is clearer through typed APIs:

```cpp
#include <dali-ui-foundation/public-api/label.h>

using namespace Dali;
using namespace Dali::Ui;

Label CreateConfiguredLabel(const Dali::String& text)
{
  return Label::New(text)
    .SetFontFamily("SamsungOneUI_400")
    .SetFontSize(18.0f)
    .SetFontWeight(Text::FontWeight::NORMAL)
    .SetHorizontalTextAlignment(Text::Alignment::START)
    .SetLineHeight(1.3f)
    .SetAsyncRendering(true);
}
```
