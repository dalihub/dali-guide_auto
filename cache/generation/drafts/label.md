---
title: Label
sidebar_label: Label
category: views-components
---

# Label

`Dali::Ui::Label` is the dali-ui view for displaying non-editable UTF-8 text in an application UI.

## Table of Contents

- [Create a Text View](#create-a-text-view)
- [Typography and Color](#typography-and-color)
- [Layout, Wrapping, and Alignment](#layout-wrapping-and-alignment)
- [Font Scaling](#font-scaling)
- [Anchor Text](#anchor-text)
- [Rendering Options](#rendering-options)
- [Inspecting Label State](#inspecting-label-state)

## Create a Text View

Use `Dali::Ui::Label::New` to create a label. In a dali-ui app, treat the label as a `Dali::Ui::View` that participates in the view tree, while configuring text-specific behavior through `Dali::Ui::Label`.

```cpp
Dali::Ui::Label title = Dali::Ui::Label::New("Settings");
```

For reusable UI construction, return the label as the concrete text view and apply typed setters in a fluent chain.

```cpp
Dali::Ui::Label CreateTitleLabel(const Dali::String& titleText)
{
  return Dali::Ui::Label::New(titleText)
    .SetFontFamily("SamsungOneUI_700")
    .SetFontSize(32.0f);
}
```

`Dali::Ui::Label` owns the label-specific text API. Use `Dali::Ui::Label::Property` constants only when integrating with property-based infrastructure; for direct application code, prefer typed setters such as `SetFontFamily`, `SetFontSize`, and `SetFontWeight`.

## Typography and Color

A label can be styled with font family, pixel font size, font weight, width, slant, variation axes, and text colors. The typed setters return `Dali::Ui::Label&`, so they can be chained.

```cpp
Dali::Ui::Label CreateBodyLabel(const Dali::String& bodyText,
                                const Dali::Ui::UiColor& textColor,
                                Dali::Ui::Text::FontWeight weight,
                                Dali::Ui::Text::FontWidth width,
                                Dali::Ui::Text::FontSlant slant)
{
  return Dali::Ui::Label::New(bodyText)
    .SetFontFamily("SamsungOneUI_400")
    .SetFontSize(18.0f)
    .SetFontWeight(weight)
    .SetFontWidth(width)
    .SetFontSlant(slant)
    .SetTextColor(textColor);
}
```

Use `SetTextBackgroundColor` when the background should be rendered behind the glyphs rather than behind the whole view.

```cpp
Dali::Ui::Label CreateHighlightedLabel(const Dali::String& text,
                                       const Dali::Ui::UiColor& textColor,
                                       const Dali::Ui::UiColor& highlightColor)
{
  return Dali::Ui::Label::New(text)
    .SetFontSize(20.0f)
    .SetTextColor(textColor)
    .SetTextBackgroundColor(highlightColor);
}
```

For variable fonts, `SetFontVariation` replaces the current variation axes. `GetFontVariation` returns the current axis list.

```cpp
void ApplyFontVariation(Dali::Ui::Label label,
                        const Dali::Vector<Dali::Ui::Text::FontVariationAxis>& axes)
{
  label.SetFontVariation(axes);

  Dali::Vector<Dali::Ui::Text::FontVariationAxis> currentAxes =
    label.GetFontVariation();
}
```

Related label-owned property constants include `Dali::Ui::Label::Property::FONT_FAMILY`, `Dali::Ui::Label::Property::FONT_SIZE`, `Dali::Ui::Label::Property::FONT_WEIGHT`, `Dali::Ui::Label::Property::FONT_WIDTH`, `Dali::Ui::Label::Property::FONT_SLANT`, `Dali::Ui::Label::Property::TEXT_COLOR`, and `Dali::Ui::Label::Property::TEXT_BACKGROUND_COLOR`.

## Layout, Wrapping, and Alignment

Use alignment setters when the label has a bounded view size and the text must be positioned within that area. `SetHorizontalTextAlignment` and `SetVerticalTextAlignment` both take `Dali::Ui::Text::Alignment`.

```cpp
Dali::Ui::Label CreateAlignedLabel(const Dali::String& text,
                                   Dali::Ui::Text::Alignment horizontal,
                                   Dali::Ui::Text::Alignment vertical)
{
  return Dali::Ui::Label::New(text)
    .SetFontSize(16.0f)
    .SetHorizontalTextAlignment(horizontal)
    .SetVerticalTextAlignment(vertical);
}
```

Line height is configured with `SetLineHeight`. The meaning of the value depends on the label's current line height mode; `GetLineHeight` returns the current numeric value.

```cpp
Dali::Ui::Label CreateReadableLabel(const Dali::String& text,
                                    Dali::Ui::Text::Alignment alignment)
{
  Dali::Ui::Label label = Dali::Ui::Label::New(text)
    .SetFontSize(18.0f)
    .SetLineHeight(1.4f)
    .SetHorizontalTextAlignment(alignment);

  float lineHeight = label.GetLineHeight();
  DALI_UNUSED(lineHeight);

  return label;
}
```

`GetLineWrapMode`, `GetHorizontalTextAlignment`, `GetVerticalTextAlignment`, and `GetLayoutDirectionMode` let application code inspect the resolved label configuration. The corresponding label property constants are `Dali::Ui::Label::Property::LINE_WRAP_MODE`, `Dali::Ui::Label::Property::HORIZONTAL_ALIGNMENT`, `Dali::Ui::Label::Property::VERTICAL_ALIGNMENT`, and `Dali::Ui::Label::Property::LAYOUT_DIRECTION_MODE`.

## Font Scaling

`SetFontSizeScale` multiplies the configured font size. Use `SetMinimumFontSizeScale` and `SetMaximumFontSizeScale` to bound the effective scale, and read the final value with `GetAdjustedFontSizeScale`.

```cpp
Dali::Ui::Label CreateScalableLabel(const Dali::String& text)
{
  return Dali::Ui::Label::New(text)
    .SetFontSize(24.0f)
    .SetFontSizeScale(1.0f)
    .SetMinimumFontSizeScale(0.8f)
    .SetMaximumFontSizeScale(1.6f);
}

float ReadEffectiveScale(Dali::Ui::Label label)
{
  return label.GetAdjustedFontSizeScale();
}
```

When system font scaling should participate in the final scale, enable it with `SetSystemFontSizeScaleEnabled`.

```cpp
void ConfigureAccessibleScale(Dali::Ui::Label label)
{
  label
    .SetSystemFontSizeScaleEnabled(true)
    .SetMinimumFontSizeScale(0.8f)
    .SetMaximumFontSizeScale(2.0f);

  float configuredScale = label.GetFontSizeScale();
  float adjustedScale = label.GetAdjustedFontSizeScale();

  DALI_UNUSED(configuredScale);
  DALI_UNUSED(adjustedScale);
}
```

The related owned property constants are `Dali::Ui::Label::Property::FONT_SIZE_SCALE`, `Dali::Ui::Label::Property::MINIMUM_FONT_SIZE_SCALE`, `Dali::Ui::Label::Property::MAXIMUM_FONT_SIZE_SCALE`, and `Dali::Ui::Label::Property::SYSTEM_FONT_SIZE_SCALE_ENABLED`.

## Anchor Text

`Dali::Ui::Label` owns anchor styling and anchor click delivery. Configure normal and clicked anchor colors with `SetAnchorColor` and `SetAnchorClickedColor`.

```cpp
Dali::Ui::Label CreateLinkedLabel(const Dali::String& markupText,
                                  const Dali::Ui::UiColor& anchorColor,
                                  const Dali::Ui::UiColor& clickedColor)
{
  return Dali::Ui::Label::New(markupText)
    .SetAnchorColor(anchorColor)
    .SetAnchorClickedColor(clickedColor);
}
```

Connect `AnchorClickedSignal` to receive the clicked `href`. The callback receives the label as a `Dali::Ui::View` and the anchor target as `Dali::String`.

```cpp
class LinkController : public Dali::ConnectionTracker
{
public:
  void Watch(Dali::Ui::Label label)
  {
    label.AnchorClickedSignal().Connect(this, &LinkController::OnAnchorClicked);
  }

private:
  void OnAnchorClicked(Dali::Ui::View view, const Dali::String& href)
  {
    Dali::Ui::Label label = Dali::Ui::Label::DownCast(view);
    if(label)
    {
      Dali::String clickedHref = href;
      DALI_UNUSED(clickedHref);
    }
  }
};
```

Use `Dali::Ui::Label::Property::ANCHOR_COLOR` and `Dali::Ui::Label::Property::ANCHOR_CLICKED_COLOR` when property integration needs the same settings.

## Rendering Options

By default, text rendering is synchronous. Enable asynchronous rendering with `SetAsyncRendering` when the label should request asynchronous text rendering during relayout. The latest asynchronous line result is available through `GetAsyncLineCount`.

```cpp
Dali::Ui::Label CreateAsyncLabel(const Dali::String& text)
{
  return Dali::Ui::Label::New(text)
    .SetFontSize(20.0f)
    .SetAsyncRendering(true);
}

int ReadAsyncLines(Dali::Ui::Label label)
{
  return label.GetAsyncLineCount();
}
```

`SetRenderScale` rasterizes text at a larger scale and downsamples it. This is useful when the view is visually scaled; the label's layout size is not changed by the render scale. The value must be `1.0f` or greater and is valid when asynchronous rendering is enabled.

```cpp
Dali::Ui::Label CreateScaledRenderLabel(const Dali::String& text)
{
  return Dali::Ui::Label::New(text)
    .SetFontSize(22.0f)
    .SetAsyncRendering(true)
    .SetRenderScale(1.5f);
}

float ReadRenderScale(Dali::Ui::Label label)
{
  return label.GetRenderScale();
}
```

`SetCutoutEnabled` renders glyph shapes as cutouts instead of filled text.

```cpp
Dali::Ui::Label CreateCutoutLabel(const Dali::String& text)
{
  return Dali::Ui::Label::New(text)
    .SetFontSize(48.0f)
    .SetCutoutEnabled(true);
}
```

Related label-owned property constants include `Dali::Ui::Label::Property::ASYNC_RENDERING`, `Dali::Ui::Label::Property::RENDER_SCALE`, `Dali::Ui::Label::Property::PIXEL_SNAP_FACTOR`, and `Dali::Ui::Label::Property::CUTOUT_ENABLED`.

## Inspecting Label State

Use getters when layout or controller code needs to mirror label settings into other application state. The getter names match the typed setters where the setting is readable.

```cpp
struct LabelTextState
{
  Dali::String text;
  Dali::String fontFamily;
  float fontSize;
  float lineHeight;
  Dali::Ui::Text::Alignment horizontalAlignment;
  Dali::Ui::Text::Alignment verticalAlignment;
};

LabelTextState ReadLabelTextState(Dali::Ui::Label label)
{
  LabelTextState state;
  state.text = label.GetText();
  state.fontFamily = label.GetFontFamily();
  state.fontSize = label.GetFontSize();
  state.lineHeight = label.GetLineHeight();
  state.horizontalAlignment = label.GetHorizontalTextAlignment();
  state.verticalAlignment = label.GetVerticalTextAlignment();
  return state;
}
```

For runtime text measurement, `GetLineCount` returns the number of lines for the current layout width. Use `GetAsyncLineCount` after asynchronous rendering or asynchronous size computation has completed.

```cpp
int ReadCurrentLineCount(Dali::Ui::Label label)
{
  return label.GetLineCount();
}
```

`Dali::Ui::Label::Property::TEXT`, `Dali::Ui::Label::Property::MULTI_LINE`, `Dali::Ui::Label::Property::OVERFLOW_MODE`, `Dali::Ui::Label::Property::LINE_HEIGHT`, and `Dali::Ui::Label::Property::LINE_HEIGHT_MODE` identify the corresponding label-owned properties for property-based tooling.
