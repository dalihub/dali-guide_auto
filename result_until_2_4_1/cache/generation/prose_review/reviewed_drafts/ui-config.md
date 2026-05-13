---
title: Ui Config
sidebar_label: Ui Config
category: styling-theme-config
---

# Ui Config

`Dali::Ui::UiConfig` defines global defaults that dali-ui uses before an application builds its `Dali::Ui::View` tree or uses UiConfig-dependent features.

## Table of Contents

- [Apply Configuration at Startup](#apply-configuration-at-startup)
- [Scaling and DPI Defaults](#scaling-and-dpi-defaults)
- [Text Defaults](#text-defaults)
- [Keyboard, Focus, and Tap Defaults](#keyboard-focus-and-tap-defaults)
- [Broken Image Fallbacks](#broken-image-fallbacks)
- [Marquee Defaults for Text](#marquee-defaults-for-text)
- [Extending Configuration Chaining](#extending-configuration-chaining)

## Apply Configuration at Startup

Create a `Dali::Ui::UiConfig` with `Dali::Ui::UiConfig::New()`, set the defaults your application needs, then call `Dali::Ui::UiConfig::Apply()` exactly once. `Apply()` freezes the config, so setter calls belong in startup code before your app starts using UiConfig-dependent features such as unit literals or its main `Dali::Ui::View` hierarchy.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ConfigureUi()
{
  Dali::Ui::UiConfig::New()
    .SetDpi(320)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.25f)
    .SetDefaultFontSize(18.0f)
    .Apply();
}
```

Use the return value from each typed setter for fluent chaining. If you need to inspect the values before applying, keep the `Dali::Ui::UiConfig` handle and call the matching getters.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ConfigureAndInspect()
{
  Dali::Ui::UiConfig config = Dali::Ui::UiConfig::New();

  config
    .SetDpi(240)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.0f);

  const int dpi = config.GetDpi();
  const int baselineDpi = config.GetBaselineDpi();
  const float scalingFactor = config.GetScalingFactor();

  (void)dpi;
  (void)baselineDpi;
  (void)scalingFactor;

  config.Apply();
}
```

## Scaling and DPI Defaults

`Dali::Ui::UiConfig::SetDpi()` sets the target display DPI, `Dali::Ui::UiConfig::SetBaselineDpi()` sets the reference DPI, and `Dali::Ui::UiConfig::SetScalingFactor()` sets the additional scale used by `spx` and `sdp` units. After `Dali::Ui::UiConfig::Apply()`, `Dali::Ui::UiConfig::GetDpiFactor()` returns `dpi / baselineDpi`, and `Dali::Ui::UiConfig::GetScaledDpiFactor()` returns that DPI factor multiplied by the scaling factor.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ConfigureDisplayScale()
{
  Dali::Ui::UiConfig config = Dali::Ui::UiConfig::New();

  config
    .SetDpi(320)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.5f)
    .Apply();

  const float dpiFactor = config.GetDpiFactor();
  const float scaledDpiFactor = config.GetScaledDpiFactor();

  (void)dpiFactor;
  (void)scaledDpiFactor;
}
```

The default values from `Dali::Ui::UiConfig::New()` are `scalingFactor = 1.0f`, `dpi = 160`, and `baselineDpi = 160`.

## Text Defaults

Use `Dali::Ui::UiConfig::SetDefaultFontSize()`, `Dali::Ui::UiConfig::SetDefaultTextColor()`, and `Dali::Ui::UiConfig::SetDefaultPlaceholderTextColor()` to set application-wide text defaults for dali-ui text controls. `Dali::Ui::UiConfig::SetShowPlaceholderTextOnFocus()` controls whether placeholder text is shown while the control has focus.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ConfigureTextDefaults()
{
  const Dali::Vector4 primaryText(0.05f, 0.05f, 0.05f, 1.0f);
  const Dali::Vector4 placeholderText(0.45f, 0.45f, 0.45f, 0.75f);

  Dali::Ui::UiConfig::New()
    .SetDefaultFontSize(16.0f)
    .SetDefaultTextColor(primaryText)
    .SetDefaultPlaceholderTextColor(placeholderText)
    .SetShowPlaceholderTextOnFocus(false)
    .SetLabelAsyncRendering(true)
    .Apply();
}
```

`Dali::Ui::UiConfig::SetLabelAsyncRendering()` sets whether `Dali::Ui::Label` uses asynchronous text rendering by default. Query the current text-related defaults with `Dali::Ui::UiConfig::GetDefaultFontSize()`, `Dali::Ui::UiConfig::GetDefaultTextColor()`, `Dali::Ui::UiConfig::GetDefaultPlaceholderTextColor()`, `Dali::Ui::UiConfig::IsPlaceholderTextShownOnFocus()`, and `Dali::Ui::UiConfig::IsLabelAsyncRendering()`.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ReadTextDefaults(Dali::Ui::UiConfig config)
{
  const float fontSize = config.GetDefaultFontSize();
  const Dali::Vector4 textColor = config.GetDefaultTextColor();
  const Dali::Vector4 placeholderColor = config.GetDefaultPlaceholderTextColor();
  const bool showPlaceholderOnFocus = config.IsPlaceholderTextShownOnFocus();
  const bool asyncLabelRendering = config.IsLabelAsyncRendering();

  (void)fontSize;
  (void)textColor;
  (void)placeholderColor;
  (void)showPlaceholderOnFocus;
  (void)asyncLabelRendering;
}
```

## Keyboard, Focus, and Tap Defaults

`Dali::Ui::UiConfig` can set defaults for key-based click execution, key long-press recognition, focus visibility, Escape-key focus clearing, and tap timing. These defaults are used by interactive dali-ui controls in the `Dali::Ui::View` tree.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

bool AcceptActivationKey(const Dali::String& keyName)
{
  return keyName == "Return" || keyName == "KP_Enter";
}

void ConfigureInputDefaults()
{
  Dali::Ui::UiConfig::New()
    .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE)
    .SetExecutionKeyPredicate(AcceptActivationKey)
    .SetKeyLongPressThreshold(3u)
    .SetTapRecognizerTime(300u)
    .EnableFocusClearOnEscape(true)
    .SetAlwaysShowFocus(true)
    .Apply();
}
```

`Dali::Ui::ExecutionKeyPredicate` is a plain function pointer with the signature `bool(const Dali::String&)`. Use a free function or a stateless lambda that can convert to that pointer type. `Dali::Ui::UiConfig::SetKeyLongPressThreshold()` takes the number of consecutive key press events required to recognize a long press, while `Dali::Ui::UiConfig::SetTapRecognizerTime()` takes a time limit in milliseconds.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ReadInputDefaults(Dali::Ui::UiConfig config)
{
  const Dali::Ui::KeyClickPolicy keyClickPolicy = config.GetKeyClickPolicy();
  const Dali::Ui::ExecutionKeyPredicate predicate = config.GetExecutionKeyPredicate();
  const uint32_t longPressThreshold = config.GetKeyLongPressThreshold();
  const uint32_t tapTime = config.GetTapRecognizerTime();
  const bool clearFocusOnEscape = config.IsFocusClearOnEscapeEnabled();
  const bool alwaysShowFocus = config.IsFocusIndicatorAlwaysShown();

  (void)keyClickPolicy;
  (void)predicate;
  (void)longPressThreshold;
  (void)tapTime;
  (void)clearFocusOnEscape;
  (void)alwaysShowFocus;
}
```

## Broken Image Fallbacks

Use `Dali::Ui::UiConfig::SetBrokenImageUrl()` to provide fallback images for failed image loading in image views. `Dali::Ui::UiConfig::BrokenImageType` has separate slots for `SMALL`, `NORMAL`, and `LARGE`, so the application can choose fallback assets that match different view sizes.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ConfigureBrokenImages()
{
  Dali::Ui::UiConfig::New()
    .SetBrokenImageUrl(
      Dali::Ui::UiConfig::BrokenImageType::SMALL,
      Dali::String("/opt/app/res/images/broken-small.png"))
    .SetBrokenImageUrl(
      Dali::Ui::UiConfig::BrokenImageType::NORMAL,
      Dali::String("/opt/app/res/images/broken-normal.png"))
    .SetBrokenImageUrl(
      Dali::Ui::UiConfig::BrokenImageType::LARGE,
      Dali::String("/opt/app/res/images/broken-large.png"))
    .Apply();
}
```

You can read back the configured URL with `Dali::Ui::UiConfig::GetBrokenImageUrl()`.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ReadBrokenImageUrl(Dali::Ui::UiConfig config)
{
  const Dali::String& normalBrokenImage =
    config.GetBrokenImageUrl(Dali::Ui::UiConfig::BrokenImageType::NORMAL);

  (void)normalBrokenImage;
}
```

## Marquee Defaults for Text

`Dali::Ui::UiConfig` also provides default marquee behavior for text. Use `Dali::Ui::UiConfig::SetMarqueeSpeed()`, `Dali::Ui::UiConfig::SetMarqueeLoopCount()`, `Dali::Ui::UiConfig::SetMarqueeLoopDelay()`, and `Dali::Ui::UiConfig::SetMarqueeGap()` to define the timing and spacing. Use `Dali::Ui::UiConfig::SetMarqueeStopMode()` and `Dali::Ui::UiConfig::SetMarqueeOrientation()` with `Dali::Ui::Text` marquee enums.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ConfigureMarqueeDefaults()
{
  Dali::Ui::UiConfig::New()
    .SetMarqueeSpeed(90)
    .SetMarqueeLoopCount(1)
    .SetMarqueeLoopDelay(0.5f)
    .SetMarqueeGap(48.0f)
    .SetMarqueeStopMode(Dali::Ui::Text::MarqueeStopMode::IMMEDIATE)
    .SetMarqueeOrientation(Dali::Ui::Text::MarqueeOrientation::HORIZONTAL)
    .Apply();
}
```

The matching getters let you read the configured defaults used by text controls.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ReadMarqueeDefaults(Dali::Ui::UiConfig config)
{
  const int speed = config.GetMarqueeSpeed();
  const int loopCount = config.GetMarqueeLoopCount();
  const float loopDelay = config.GetMarqueeLoopDelay();
  const float gap = config.GetMarqueeGap();
  const Dali::Ui::Text::MarqueeStopMode stopMode = config.GetMarqueeStopMode();
  const Dali::Ui::Text::MarqueeOrientation orientation = config.GetMarqueeOrientation();

  (void)speed;
  (void)loopCount;
  (void)loopDelay;
  (void)gap;
  (void)stopMode;
  (void)orientation;
}
```

## Extending Configuration Chaining

`ui-config.autogen.h` is part of the public dali-ui configuration surface. It provides `DALI_UI_CHAIN_UICONFIG_METHODS`, a macro used by config types that derive from `Dali::Ui::UiConfig` and want the chain-generated `Dali::Ui::UiConfig` setters to return the derived type for fluent chaining.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

class ProductUiConfig : public Dali::Ui::UiConfig
{
public:
  DALI_UI_CHAIN_UICONFIG_METHODS(ProductUiConfig)

  static ProductUiConfig New();
};
```

A derived config can then expose product-specific setup while keeping the same `Dali::Ui::UiConfig::Apply()` flow.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ConfigureProductUi(ProductUiConfig config)
{
  config
    .SetDpi(320)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.25f)
    .SetKeyLongPressThreshold(4u)
    .Apply();
}
```
