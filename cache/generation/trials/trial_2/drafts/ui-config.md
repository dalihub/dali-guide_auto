---
title: Ui Config
sidebar_label: Ui Config
category: styling-theme-config
---

# Ui Config

`Dali::Ui::UiConfig` applies process-wide dali-ui defaults before a dali-ui application starts building its `Dali::Ui::View` tree.

## Table of Contents

- [Apply the dali-ui configuration](#apply-the-dali-ui-configuration)
- [Scale app dimensions consistently](#scale-app-dimensions-consistently)
- [Set keyboard and focus behavior](#set-keyboard-and-focus-behavior)
- [Set text and input defaults](#set-text-and-input-defaults)
- [Configure marquee defaults](#configure-marquee-defaults)
- [Configure broken image fallbacks](#configure-broken-image-fallbacks)
- [Read back applied values](#read-back-applied-values)
- [Generated chaining support](#generated-chaining-support)

## Apply the dali-ui configuration

Create the global configuration with `Dali::Ui::UiConfig::New`, set the values your application needs, and finish with `Dali::Ui::UiConfig::Apply`.

Call `Dali::Ui::UiConfig::Apply` once during application startup, before creating the main dali-ui `Dali::Ui::View` hierarchy. Setter methods are intended for the pre-apply phase; after `Dali::Ui::UiConfig::Apply`, the configuration is frozen.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

using namespace Dali;
using namespace Dali::Ui;

void ConfigureUi()
{
  UiConfig::New()
    .SetDpi(320)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.25f)
    .Apply();
}
```

Use the returned reference from each setter for fluent configuration. `Dali::Ui::UiConfig::SetDpi`, `Dali::Ui::UiConfig::SetBaselineDpi`, and `Dali::Ui::UiConfig::SetScalingFactor` all return `Dali::Ui::UiConfig&`, so they can be chained directly before `Dali::Ui::UiConfig::Apply`.

## Scale app dimensions consistently

`Dali::Ui::UiConfig` owns the global scaling inputs used by dali-ui unit calculations. The default values from `Dali::Ui::UiConfig::New` are `scalingFactor=1.0f`, `dpi=160`, and `baselineDpi=160`.

Use `Dali::Ui::UiConfig::SetDpi` for the target display DPI, `Dali::Ui::UiConfig::SetBaselineDpi` for the reference DPI, and `Dali::Ui::UiConfig::SetScalingFactor` for application-level scale adjustment.

```cpp
using namespace Dali::Ui;

UiConfig CreatePhoneScaleConfig()
{
  UiConfig config = UiConfig::New();

  config
    .SetDpi(320)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.15f);

  return config;
}

void ApplyPhoneScaleConfig()
{
  CreatePhoneScaleConfig().Apply();
}
```

After the config is applied, `Dali::Ui::UiConfig::GetDpiFactor` returns the computed `dpi / baselineDpi` factor, and `Dali::Ui::UiConfig::GetScaledDpiFactor` returns the DPI factor multiplied by `Dali::Ui::UiConfig::GetScalingFactor`.

```cpp
using namespace Dali::Ui;

float ReadEffectiveScale(const UiConfig& config)
{
  const float dpiFactor       = config.GetDpiFactor();
  const float scaledDpiFactor = config.GetScaledDpiFactor();

  return dpiFactor * scaledDpiFactor;
}
```

## Set keyboard and focus behavior

`Dali::Ui::UiConfig` provides defaults for keyboard-driven activation and focus display in the `Dali::Ui::View` tree.

Use `Dali::Ui::UiConfig::SetKeyClickPolicy` to set the default key-click policy for clickable views. Use `Dali::Ui::UiConfig::SetExecutionKeyPredicate` when the application needs a custom key-name predicate for click execution. The predicate type is `Dali::Ui::ExecutionKeyPredicate`, a plain function pointer taking `const Dali::String&` and returning `bool`.

```cpp
#include <dali/public-api/common/dali-string.h>
#include <dali-ui-foundation/public-api/ui-config.h>

using namespace Dali;
using namespace Dali::Ui;

bool IsConfirmKey(const Dali::String& keyName)
{
  return keyName == "Return" || keyName == "KP_Enter";
}

void ConfigureKeyInput(KeyClickPolicy policy)
{
  UiConfig::New()
    .SetKeyClickPolicy(policy)
    .SetExecutionKeyPredicate(IsConfirmKey)
    .SetKeyLongPressThreshold(3u)
    .SetTapRecognizerTime(180u)
    .Apply();
}
```

Use `Dali::Ui::UiConfig::EnableFocusClearOnEscape` to enable focus clearing from Escape, and `Dali::Ui::UiConfig::SetAlwaysShowFocus` to request that the keyboard focus indicator remains visible.

```cpp
using namespace Dali::Ui;

void ConfigureFocusDefaults()
{
  UiConfig::New()
    .EnableFocusClearOnEscape(true)
    .SetAlwaysShowFocus(true)
    .Apply();
}
```

## Set text and input defaults

Text-related defaults are configured once on `Dali::Ui::UiConfig` and then used by dali-ui text controls created after the global configuration is active.

Use `Dali::Ui::UiConfig::SetDefaultFontSize` for the default font point size, `Dali::Ui::UiConfig::SetDefaultTextColor` for normal text color, and `Dali::Ui::UiConfig::SetLabelAsyncRendering` for the default label rendering mode.

```cpp
#include <dali/public-api/math/vector4.h>
#include <dali-ui-foundation/public-api/ui-config.h>

using namespace Dali;
using namespace Dali::Ui;

void ConfigureTextDefaults()
{
  UiConfig::New()
    .SetDefaultFontSize(18.0f)
    .SetDefaultTextColor(Vector4(0.05f, 0.05f, 0.05f, 1.0f))
    .SetLabelAsyncRendering(true)
    .Apply();
}
```

For input placeholder styling, use `Dali::Ui::UiConfig::SetDefaultPlaceholderTextColor` and `Dali::Ui::UiConfig::SetShowPlaceholderTextOnFocus`.

```cpp
using namespace Dali;
using namespace Dali::Ui;

void ConfigurePlaceholderDefaults()
{
  UiConfig::New()
    .SetDefaultPlaceholderTextColor(Vector4(0.45f, 0.45f, 0.45f, 1.0f))
    .SetShowPlaceholderTextOnFocus(false)
    .Apply();
}
```

You can inspect the configured defaults with `Dali::Ui::UiConfig::GetDefaultFontSize`, `Dali::Ui::UiConfig::GetDefaultTextColor`, `Dali::Ui::UiConfig::GetDefaultPlaceholderTextColor`, `Dali::Ui::UiConfig::IsPlaceholderTextShownOnFocus`, and `Dali::Ui::UiConfig::IsLabelAsyncRendering`.

```cpp
using namespace Dali;
using namespace Dali::Ui;

bool UsesAsyncTextWithVisiblePlaceholder(const UiConfig& config)
{
  const bool asyncText         = config.IsLabelAsyncRendering();
  const bool placeholderFocus = config.IsPlaceholderTextShownOnFocus();

  return asyncText && placeholderFocus;
}
```

## Configure marquee defaults

`Dali::Ui::UiConfig` also owns default marquee behavior for dali-ui text usage. Configure these defaults before `Dali::Ui::UiConfig::Apply`.

Use `Dali::Ui::UiConfig::SetMarqueeSpeed` for pixels per second, `Dali::Ui::UiConfig::SetMarqueeLoopCount` for complete loops, `Dali::Ui::UiConfig::SetMarqueeLoopDelay` for the delay in seconds, and `Dali::Ui::UiConfig::SetMarqueeGap` for the wrap gap in pixels.

```cpp
using namespace Dali::Ui;

void ConfigureMarqueeTiming()
{
  UiConfig::New()
    .SetLabelAsyncRendering(true)
    .SetMarqueeSpeed(80)
    .SetMarqueeLoopCount(2)
    .SetMarqueeLoopDelay(0.5f)
    .SetMarqueeGap(24.0f)
    .Apply();
}
```

`Dali::Ui::UiConfig::SetMarqueeOrientation` accepts a `Dali::Ui::Text::MarqueeOrientation`, and `Dali::Ui::UiConfig::SetMarqueeStopMode` accepts a `Dali::Ui::Text::MarqueeStopMode`. This lets the app set one default orientation and stop behavior for marquee-enabled text.

```cpp
using namespace Dali::Ui;

void ConfigureMarqueeShape(Text::MarqueeOrientation orientation,
                           Text::MarqueeStopMode stopMode)
{
  UiConfig::New()
    .SetMarqueeOrientation(orientation)
    .SetMarqueeStopMode(stopMode)
    .Apply();
}
```

Read these values back with `Dali::Ui::UiConfig::GetMarqueeSpeed`, `Dali::Ui::UiConfig::GetMarqueeLoopCount`, `Dali::Ui::UiConfig::GetMarqueeLoopDelay`, `Dali::Ui::UiConfig::GetMarqueeGap`, `Dali::Ui::UiConfig::GetMarqueeOrientation`, and `Dali::Ui::UiConfig::GetMarqueeStopMode`.

```cpp
using namespace Dali::Ui;

float ComputeMarqueeTravelPadding(const UiConfig& config)
{
  return config.GetMarqueeGap() + static_cast<float>(config.GetMarqueeSpeed());
}
```

## Configure broken image fallbacks

Use `Dali::Ui::UiConfig::SetBrokenImageUrl` to register fallback image URLs for image loading failures. The type parameter is `Dali::Ui::UiConfig::BrokenImageType`, allowing separate fallback assets for different view sizes.

```cpp
#include <dali/public-api/common/dali-string.h>
#include <dali-ui-foundation/public-api/ui-config.h>

using namespace Dali;
using namespace Dali::Ui;

void ConfigureBrokenImages(const Dali::String& smallUrl,
                           const Dali::String& normalUrl,
                           const Dali::String& largeUrl)
{
  UiConfig::New()
    .SetBrokenImageUrl(UiConfig::BrokenImageType::SMALL, smallUrl)
    .SetBrokenImageUrl(UiConfig::BrokenImageType::NORMAL, normalUrl)
    .SetBrokenImageUrl(UiConfig::BrokenImageType::LARGE, largeUrl)
    .Apply();
}
```

Use `Dali::Ui::UiConfig::GetBrokenImageUrl` to retrieve the URL for a specific `Dali::Ui::UiConfig::BrokenImageType`.

```cpp
using namespace Dali;
using namespace Dali::Ui;

const Dali::String& SelectLargeBrokenImage(const UiConfig& config)
{
  return config.GetBrokenImageUrl(UiConfig::BrokenImageType::LARGE);
}
```

## Read back applied values

A `Dali::Ui::UiConfig` handle can be copied or assigned like other DALi handles. Use `Dali::Ui::UiConfig::DownCast` when you receive a `Dali::BaseHandle` and need to recover a `Dali::Ui::UiConfig`.

```cpp
#include <dali/public-api/object/base-handle.h>
#include <dali-ui-foundation/public-api/ui-config.h>

using namespace Dali;
using namespace Dali::Ui;

int ReadConfiguredDpi(BaseHandle handle)
{
  UiConfig config = UiConfig::DownCast(handle);

  if(config)
  {
    return config.GetDpi();
  }

  return 0;
}
```

For diagnostics or application setup checks, the scalar getters mirror the corresponding setters: `Dali::Ui::UiConfig::GetDpi`, `Dali::Ui::UiConfig::GetBaselineDpi`, `Dali::Ui::UiConfig::GetScalingFactor`, `Dali::Ui::UiConfig::GetTapRecognizerTime`, and `Dali::Ui::UiConfig::GetKeyLongPressThreshold`.

```cpp
using namespace Dali::Ui;

bool HasExpectedScaleInputs(const UiConfig& config)
{
  return config.GetDpi() > 0 &&
         config.GetBaselineDpi() > 0 &&
         config.GetScalingFactor() > 0.0f;
}
```

## Generated chaining support

`ui-config.autogen.h` is part of the `Dali::Ui::UiConfig` feature surface. It defines `DALI_UI_CHAIN_UICONFIG_METHODS`, a generated helper macro that forwards selected `Dali::Ui::UiConfig` setters and returns the derived `ChildClass&`.

Application code normally uses `Dali::Ui::UiConfig` directly. The generated chaining helper is useful when a config-like subclass exposes the same fluent setup style.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

using namespace Dali::Ui;

class AppUiConfig : public UiConfig
{
public:
  DALI_UI_CHAIN_UICONFIG_METHODS(AppUiConfig)

  static AppUiConfig New();
};

void ApplyCustomConfig(AppUiConfig config)
{
  config
    .SetDpi(320)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.2f)
    .Apply();
}
```

The generated methods include chainable forwarding for `Dali::Ui::UiConfig::SetScalingFactor`, `Dali::Ui::UiConfig::SetDpi`, `Dali::Ui::UiConfig::SetBaselineDpi`, `Dali::Ui::UiConfig::SetKeyClickPolicy`, `Dali::Ui::UiConfig::SetExecutionKeyPredicate`, `Dali::Ui::UiConfig::SetKeyLongPressThreshold`, `Dali::Ui::UiConfig::SetTapRecognizerTime`, `Dali::Ui::UiConfig::SetBrokenImageUrl`, `Dali::Ui::UiConfig::EnableFocusClearOnEscape`, and `Dali::Ui::UiConfig::SetDefaultInteractionEffect`.
