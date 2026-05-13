---
title: Ui Config
sidebar_label: Ui Config
category: styling-theme-config
---

# Ui Config

`Dali::Ui::UiConfig` defines global dali-ui application defaults before app code uses dali-ui configuration-dependent features.

## Table of Contents

- [Configure dali-ui at Startup](#configure-dali-ui-at-startup)
- [Scaling and DPI Defaults](#scaling-and-dpi-defaults)
- [Text Defaults](#text-defaults)
- [Input and Focus Defaults](#input-and-focus-defaults)
- [Image Fallbacks](#image-fallbacks)
- [Marquee Defaults](#marquee-defaults)
- [Reading the Applied Configuration](#reading-the-applied-configuration)
- [Extending Configuration with `ui-config.autogen.h`](#extending-configuration-with-ui-configautogenh)

## Configure dali-ui at Startup

Create a `Dali::Ui::UiConfig` with `Dali::Ui::UiConfig::New()`, set the application defaults you need, then call `Dali::Ui::UiConfig::Apply()`. Setter methods return `Dali::Ui::UiConfig&`, so startup configuration is normally written as a fluent chain.

Call `Dali::Ui::UiConfig::Apply()` exactly once during application startup, typically before the application main loop starts and before application code depends on dali-ui global defaults. After `Dali::Ui::UiConfig::Apply()`, the configuration is frozen and setter methods must not be called.

```cpp
void ConfigureDaliUi()
{
  Dali::Ui::UiConfig::New()
    .SetDpi(320)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.25f)
    .SetDefaultFontSize(18.0f)
    .Apply();
}
```

Use the resulting defaults as application-level policy for the `Dali::Ui::View` hierarchy. Per-view settings can still be chosen by the relevant view APIs, but `Dali::Ui::UiConfig` is where you establish the app baseline.

```cpp
Dali::Ui::UiConfig CreateDefaultConfig()
{
  return Dali::Ui::UiConfig::New()
    .SetDpi(240)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.0f);
}

void ConfigureDaliUi()
{
  Dali::Ui::UiConfig config = CreateDefaultConfig();
  config.Apply();
}
```

## Scaling and DPI Defaults

`Dali::Ui::UiConfig::SetDpi()` sets the target display DPI. `Dali::Ui::UiConfig::SetBaselineDpi()` sets the reference DPI. `Dali::Ui::UiConfig::SetScalingFactor()` sets the scale multiplier used by `spx` and `sdp` units.

`Dali::Ui::UiConfig::GetDpiFactor()` returns `dpi / baselineDpi`, and `Dali::Ui::UiConfig::GetScaledDpiFactor()` returns that DPI factor multiplied by the scaling factor. These computed factor getters require `Dali::Ui::UiConfig::Apply()` to have been called.

```cpp
Dali::Ui::UiConfig ConfigureScale()
{
  Dali::Ui::UiConfig config = Dali::Ui::UiConfig::New()
    .SetDpi(320)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.5f);

  config.Apply();

  const int dpi = config.GetDpi();
  const int baselineDpi = config.GetBaselineDpi();
  const float scalingFactor = config.GetScalingFactor();
  const float dpiFactor = config.GetDpiFactor();
  const float scaledDpiFactor = config.GetScaledDpiFactor();

  return config;
}
```

## Text Defaults

Use `Dali::Ui::UiConfig::SetDefaultFontSize()` and `Dali::Ui::UiConfig::SetDefaultTextColor()` to define default text values for dali-ui text elements. Use `Dali::Ui::UiConfig::SetDefaultPlaceholderTextColor()` and `Dali::Ui::UiConfig::SetShowPlaceholderTextOnFocus()` to define hint text behavior.

`Dali::Ui::UiConfig::SetLabelAsyncRendering()` controls whether `Dali::Ui::Label` uses asynchronous text rendering by default.

```cpp
void ConfigureTextDefaults()
{
  Dali::Ui::UiConfig::New()
    .SetDefaultFontSize(16.0f)
    .SetDefaultTextColor(Dali::Vector4(0.0f, 0.0f, 0.0f, 1.0f))
    .SetDefaultPlaceholderTextColor(Dali::Vector4(0.55f, 0.55f, 0.55f, 1.0f))
    .SetShowPlaceholderTextOnFocus(false)
    .SetLabelAsyncRendering(true)
    .Apply();
}
```

Read the same settings through typed getters when you need to pass the configured values to setup code.

```cpp
void ReadTextDefaults(const Dali::Ui::UiConfig& config)
{
  const float defaultFontSize = config.GetDefaultFontSize();
  const Dali::Vector4 defaultTextColor = config.GetDefaultTextColor();
  const Dali::Vector4 placeholderColor = config.GetDefaultPlaceholderTextColor();
  const bool showPlaceholderOnFocus = config.IsPlaceholderTextShownOnFocus();
  const bool asyncLabelRendering = config.IsLabelAsyncRendering();
}
```

## Input and Focus Defaults

`Dali::Ui::UiConfig` also owns keyboard and focus policy for the app. `Dali::Ui::UiConfig::SetKeyClickPolicy()` sets the default key click policy used by clickable views. `Dali::Ui::UiConfig::SetExecutionKeyPredicate()` sets the key-name predicate used to decide whether a key performs click execution. `Dali::Ui::UiConfig::SetKeyLongPressThreshold()` sets the repeated-key count for recognizing a long press, and `Dali::Ui::UiConfig::SetTapRecognizerTime()` sets the tap recognizer time limit in milliseconds.

The execution-key predicate type is `Dali::Ui::ExecutionKeyPredicate`, a plain function pointer taking `const Dali::String&` and returning `bool`. Use a free function or a stateless lambda; capturing lambdas and member function pointers are not accepted.

```cpp
bool IsAppExecutionKey(const Dali::String& keyName)
{
  return keyName == "Return";
}

void ConfigureInputDefaults()
{
  Dali::Ui::UiConfig::New()
    .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE)
    .SetExecutionKeyPredicate(IsAppExecutionKey)
    .SetKeyLongPressThreshold(4u)
    .SetTapRecognizerTime(250u)
    .EnableFocusClearOnEscape(true)
    .SetAlwaysShowFocus(true)
    .Apply();
}
```

Use the matching getters when app setup needs to branch on the configured input policy.

```cpp
void ReadInputDefaults(const Dali::Ui::UiConfig& config)
{
  const Dali::Ui::KeyClickPolicy keyClickPolicy = config.GetKeyClickPolicy();
  Dali::Ui::ExecutionKeyPredicate predicate = config.GetExecutionKeyPredicate();
  const uint32_t longPressThreshold = config.GetKeyLongPressThreshold();
  const uint32_t tapTimeMs = config.GetTapRecognizerTime();
  const bool clearFocusOnEscape = config.IsFocusClearOnEscapeEnabled();
  const bool alwaysShowFocus = config.IsFocusIndicatorAlwaysShown();

  const bool returnExecutes = predicate(Dali::String("Return"));
}
```

## Image Fallbacks

Use `Dali::Ui::UiConfig::SetBrokenImageUrl()` to provide fallback image URLs for image loading failures. The size category is selected with `Dali::Ui::UiConfig::BrokenImageType`.

```cpp
void ConfigureImageFallbacks()
{
  Dali::Ui::UiConfig::New()
    .SetBrokenImageUrl(
      Dali::Ui::UiConfig::BrokenImageType::SMALL,
      Dali::String("/opt/app/res/broken-small.png"))
    .SetBrokenImageUrl(
      Dali::Ui::UiConfig::BrokenImageType::NORMAL,
      Dali::String("/opt/app/res/broken-normal.png"))
    .SetBrokenImageUrl(
      Dali::Ui::UiConfig::BrokenImageType::LARGE,
      Dali::String("/opt/app/res/broken-large.png"))
    .Apply();
}
```

Retrieve a configured fallback URL with `Dali::Ui::UiConfig::GetBrokenImageUrl()`.

```cpp
Dali::String GetNormalBrokenImageUrl(const Dali::Ui::UiConfig& config)
{
  return config.GetBrokenImageUrl(Dali::Ui::UiConfig::BrokenImageType::NORMAL);
}
```

## Marquee Defaults

`Dali::Ui::UiConfig` owns default marquee behavior for dali-ui text features. Configure speed in pixels per second, loop count, loop delay in seconds, wrap gap in pixels, stop mode, and orientation before calling `Dali::Ui::UiConfig::Apply()`.

```cpp
void ConfigureMarqueeDefaults(Dali::Ui::Text::MarqueeStopMode stopMode,
                              Dali::Ui::Text::MarqueeOrientation orientation)
{
  Dali::Ui::UiConfig::New()
    .SetMarqueeSpeed(90)
    .SetMarqueeLoopCount(1)
    .SetMarqueeLoopDelay(0.5f)
    .SetMarqueeGap(48.0f)
    .SetMarqueeStopMode(stopMode)
    .SetMarqueeOrientation(orientation)
    .Apply();
}
```

The getters return the same typed values.

```cpp
void ReadMarqueeDefaults(const Dali::Ui::UiConfig& config)
{
  const int speed = config.GetMarqueeSpeed();
  const int loopCount = config.GetMarqueeLoopCount();
  const float loopDelay = config.GetMarqueeLoopDelay();
  const float gap = config.GetMarqueeGap();
  const Dali::Ui::Text::MarqueeStopMode stopMode = config.GetMarqueeStopMode();
  const Dali::Ui::Text::MarqueeOrientation orientation = config.GetMarqueeOrientation();
}
```

## Reading the Applied Configuration

A `Dali::Ui::UiConfig` handle remains usable after `Dali::Ui::UiConfig::Apply()` for getter calls. This is useful when setup code needs to log, mirror, or pass the configured values to another application layer.

```cpp
Dali::Ui::UiConfig ConfigureAndKeepHandle()
{
  Dali::Ui::UiConfig config = Dali::Ui::UiConfig::New()
    .SetDpi(320)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.0f)
    .SetDefaultFontSize(16.0f);

  config.Apply();

  const int dpi = config.GetDpi();
  const int baselineDpi = config.GetBaselineDpi();
  const float fontSize = config.GetDefaultFontSize();

  return config;
}
```

`Dali::Ui::UiConfig::DownCast()` converts a `Dali::BaseHandle` back to `Dali::Ui::UiConfig` when the handle actually refers to a config object. If it does not, the returned handle is uninitialized.

```cpp
Dali::Ui::UiConfig GetConfigFromHandle(Dali::BaseHandle handle)
{
  return Dali::Ui::UiConfig::DownCast(handle);
}
```

## Extending Configuration with `ui-config.autogen.h`

`ui-config.autogen.h` is part of the `Dali::Ui::UiConfig` feature surface. It provides `DALI_UI_CHAIN_UICONFIG_METHODS`, a macro used by config-derived classes to expose selected chainable forwarding setters that return the derived type.

Use it only in a higher-level configuration type that already has a valid `Dali::Ui::UiConfig` implementation handle. The macro preserves fluent calls for forwarded `Dali::Ui::UiConfig` setters; it does not create or initialize the config object by itself.

```cpp
class ProductUiConfig : public Dali::Ui::UiConfig
{
public:
  DALI_UI_CHAIN_UICONFIG_METHODS(ProductUiConfig)
};
```

A derived config class using `DALI_UI_CHAIN_UICONFIG_METHODS` can keep setup concise while preserving the same underlying `Dali::Ui::UiConfig` behavior: configure first, then call `Dali::Ui::UiConfig::Apply()` once before using dali-ui configuration-dependent features.
