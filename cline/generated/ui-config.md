---
title: Ui Config
sidebar_label: Ui Config
category: styling-theme-config
---

# Ui Config

`UiConfig` provides a centralized, immutable-after-init configuration object for dali-ui global settings. It holds display and scaling parameters that affect unit calculations (spx, dp, sdp) and default behaviors across the entire dali-ui framework.

## Table of Contents

- [Basic Setup and Application](#basic-setup-and-application)
- [Display Scaling and DPI Configuration](#display-scaling-and-dpi-configuration)
- [Text and Label Defaults](#text-and-label-defaults)
- [Marquee Animation Defaults](#marquee-animation-defaults)
- [Focus and Input Configuration](#focus-and-input-configuration)
- [Image Loading Error Handling](#image-loading-error-handling)
- [Interaction Effects](#interaction-effects)

## Basic Setup and Application

`UiConfig` must be created and applied exactly once, typically in `main()` before the application main loop starts. After `Apply()` is called, the configuration becomes frozen and setter methods can no longer be used.

Create a new `UiConfig` using `UiConfig::New()`, configure it using fluent method chaining, and apply it with `Apply()`:

```cpp
int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);

  UiConfig::New()
    .SetScalingFactor(1.5f)
    .SetDpi(320)
    .Apply();

  MyController controller(application);
  application.MainLoop();

  return 0;
}
```

The simplest configuration applies default values without any customization:

```cpp
UiConfig::New().Apply();
```

All setter methods return a reference to the `UiConfig` object, enabling fluent chaining. The default values are: `scalingFactor=1.0f`, `dpi=160`, `baselineDpi=160`.

## Display Scaling and DPI Configuration

`UiConfig` controls how dali-ui calculates device-independent units (dp, sdp, spx) for responsive layouts across different screen densities.

### Scaling Factor

The scaling factor affects spx and sdp unit calculations. Use `SetScalingFactor()` to configure it:

```cpp
UiConfig::New()
  .SetScalingFactor(1.5f)
  .Apply();
```

Retrieve the current scaling factor with `GetScalingFactor()`:

```cpp
float scale = config.GetScalingFactor();
```

### DPI Configuration

Set the screen DPI with `SetDpi()` for dp and sdp unit calculations:

```cpp
UiConfig::New()
  .SetDpi(320)
  .Apply();
```

The baseline DPI serves as the reference for dp calculations. Set it with `SetBaselineDpi()`:

```cpp
UiConfig::New()
  .SetBaselineDpi(160)
  .SetDpi(320)
  .Apply();
```

### DPI Factor Queries

After applying the configuration, retrieve computed factors:

- `GetDpiFactor()` returns `dpi / baselineDpi`
- `GetScaledDpiFactor()` returns `dpiFactor * scalingFactor`
- `GetDpi()` returns the screen DPI
- `GetBaselineDpi()` returns the baseline DPI

```cpp
// After Apply() has been called
float dpiFactor = config.GetDpiFactor();
float scaledDpiFactor = config.GetScaledDpiFactor();
int dpi = config.GetDpi();
int baselineDpi = config.GetBaselineDpi();
```

## Text and Label Defaults

`UiConfig` provides global defaults for text rendering and label behavior.

### Default Font Size

Set the default font point-size for text elements with `SetDefaultFontSize()`:

```cpp
UiConfig::New()
  .SetDefaultFontSize(16.0f)
  .Apply();
```

Retrieve the default with `GetDefaultFontSize()`:

```cpp
float fontSize = config.GetDefaultFontSize();
```

### Default Text Colors

Configure default text color with `SetDefaultTextColor()`:

```cpp
UiConfig::New()
  .SetDefaultTextColor(Vector4(0.2f, 0.2f, 0.2f, 1.0f))
  .Apply();
```

Set the default placeholder text color (used in `InputField` and similar controls) with `SetDefaultPlaceholderTextColor()`:

```cpp
UiConfig::New()
  .SetDefaultPlaceholderTextColor(Vector4(0.5f, 0.5f, 0.5f, 1.0f))
  .Apply();
```

Retrieve these values with `GetDefaultTextColor()` and `GetDefaultPlaceholderTextColor()`:

```cpp
Vector4 textColor = config.GetDefaultTextColor();
Vector4 placeholderColor = config.GetDefaultPlaceholderTextColor();
```

### Placeholder Text on Focus

Control whether placeholder text remains visible when a control has focus using `SetShowPlaceholderTextOnFocus()`:

```cpp
UiConfig::New()
  .SetShowPlaceholderTextOnFocus(false)
  .Apply();
```

Check the current setting with `IsPlaceholderTextShownOnFocus()`:

```cpp
bool shownOnFocus = config.IsPlaceholderTextShownOnFocus();
```

### Asynchronous Label Rendering

Enable asynchronous text rendering for `Label` controls by default with `SetLabelAsyncRendering()`. This improves performance for complex text layouts:

```cpp
UiConfig::New()
  .SetLabelAsyncRendering(true)
  .Apply();
```

Check the setting with `IsLabelAsyncRendering()`:

```cpp
bool asyncEnabled = config.IsLabelAsyncRendering();
```

## Marquee Animation Defaults

`UiConfig` provides global defaults for marquee text animation behavior in `Label` controls.

### Speed and Looping

Set the marquee speed in pixels per second with `SetMarqueeSpeed()`:

```cpp
UiConfig::New()
  .SetMarqueeSpeed(100)
  .Apply();
```

Configure the number of complete loops with `SetMarqueeLoopCount()`:

```cpp
UiConfig::New()
  .SetMarqueeLoopCount(2)
  .Apply();
```

Set the delay before marquee starts and between loops with `SetMarqueeLoopDelay()`:

```cpp
UiConfig::New()
  .SetMarqueeLoopDelay(1.0f)
  .Apply();
```

Set the gap before the marquee wraps with `SetMarqueeGap()`:

```cpp
UiConfig::New()
  .SetMarqueeGap(50.0f)
  .Apply();
```

### Stop Behavior and Orientation

Configure how marquee stops with `SetMarqueeStopMode()`:

```cpp
UiConfig::New()
  .SetMarqueeStopMode(Text::MarqueeStopMode::IMMEDIATE)
  .Apply();
```

Set the marquee scroll direction with `SetMarqueeOrientation()`:

```cpp
UiConfig::New()
  .SetMarqueeOrientation(Text::MarqueeOrientation::HORIZONTAL)
  .Apply();
```

### Retrieving Marquee Settings

Query marquee defaults with the corresponding getters:

```cpp
int speed = config.GetMarqueeSpeed();
int loopCount = config.GetMarqueeLoopCount();
float loopDelay = config.GetMarqueeLoopDelay();
float gap = config.GetMarqueeGap();
Text::MarqueeStopMode stopMode = config.GetMarqueeStopMode();
Text::MarqueeOrientation orientation = config.GetMarqueeOrientation();
```

## Focus and Input Configuration

`UiConfig` provides settings for keyboard focus behavior and input recognition.

### Focus Indicator Visibility

Control whether the keyboard focus indicator is always shown with `SetAlwaysShowFocus()`:

```cpp
UiConfig::New()
  .SetAlwaysShowFocus(true)
  .Apply();
```

Check the setting with `IsFocusIndicatorAlwaysShown()`:

```cpp
bool alwaysShown = config.IsFocusIndicatorAlwaysShown();
```

### Focus Clear on Escape

Enable automatic focus clearing when the Escape key is pressed with `EnableFocusClearOnEscape()`:

```cpp
UiConfig::New()
  .EnableFocusClearOnEscape(true)
  .Apply();
```

Check the setting with `IsFocusClearOnEscapeEnabled()`:

```cpp
bool enabled = config.IsFocusClearOnEscapeEnabled();
```

### Key Click Policy

Set the default key click policy for clickable views with `SetKeyClickPolicy()`:

```cpp
UiConfig::New()
  .SetKeyClickPolicy(KeyClickPolicy::ON_RELEASE)
  .Apply();
```

Retrieve the policy with `GetKeyClickPolicy()`:

```cpp
KeyClickPolicy policy = config.GetKeyClickPolicy();
```

### Execution Key Predicate

Define which keys trigger click execution on focused clickable views using `SetExecutionKeyPredicate()`. The predicate is a function pointer that takes a key name and returns `true` if the key should trigger a click:

```cpp
bool MyKeyPredicate(const Dali::String& keyName) {
  return keyName == "Return" || keyName == "KP_Enter";
}

int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);

  UiConfig::New()
    .SetExecutionKeyPredicate(MyKeyPredicate)
    .Apply();

  // ...
}
```

Retrieve the current predicate with `GetExecutionKeyPredicate()`. Pass `nullptr` to restore the default predicate (matches "Return" only).

### Key Long Press Threshold

Set the number of consecutive key press events required to recognize a long-press with `SetKeyLongPressThreshold()`:

```cpp
UiConfig::New()
  .SetKeyLongPressThreshold(5)
  .Apply();
```

Retrieve the threshold with `GetKeyLongPressThreshold()`:

```cpp
uint32_t threshold = config.GetKeyLongPressThreshold();
```

### Tap Recognition

Configure the tap recognizer time limit in milliseconds with `SetTapRecognizerTime()`:

```cpp
UiConfig::New()
  .SetTapRecognizerTime(300)
  .Apply();
```

Retrieve the time limit with `GetTapRecognizerTime()`:

```cpp
uint32_t timeMs = config.GetTapRecognizerTime();
```

## Image Loading Error Handling

Configure fallback images to display when image loading fails using broken image URLs. Different image types allow appropriate fallbacks based on view size.

### Setting Broken Image URLs

Use `SetBrokenImageUrl()` with a `BrokenImageType` to specify fallback images:

```cpp
UiConfig::New()
  .SetBrokenImageUrl(UiConfig::BrokenImageType::SMALL, "images/broken-small.png")
  .SetBrokenImageUrl(UiConfig::BrokenImageType::NORMAL, "images/broken-normal.png")
  .SetBrokenImageUrl(UiConfig::BrokenImageType::LARGE, "images/broken-large.png")
  .Apply();
```

The `BrokenImageType` enum provides three sizes:

- `BrokenImageType::SMALL` — For small-sized views (e.g., icons)
- `BrokenImageType::NORMAL` — For normal-sized views
- `BrokenImageType::LARGE` — For large-sized views (e.g., full-screen images)

### Retrieving Broken Image URLs

Get the configured broken image URL with `GetBrokenImageUrl()`:

```cpp
const Dali::String& smallUrl = config.GetBrokenImageUrl(UiConfig::BrokenImageType::SMALL);
const Dali::String& normalUrl = config.GetBrokenImageUrl(UiConfig::BrokenImageType::NORMAL);
const Dali::String& largeUrl = config.GetBrokenImageUrl(UiConfig::BrokenImageType::LARGE);
```

## Interaction Effects

Set a default interaction effect that applies to views when `AsInteractive()` is called.

### Setting Default Interaction Effect

Use `SetDefaultInteractionEffect()` with a `Trait` implementing `InteractionEffectInterface`:

```cpp
UiConfig::New()
  .SetDefaultInteractionEffect(myInteractionEffectTrait)
  .Apply();
```

Pass an uninitialized `Trait{}` to disable the default interaction effect.

### Retrieving the Default Effect

Get the current default interaction effect with `GetDefaultInteractionEffect()`:

```cpp
Trait effect = config.GetDefaultInteractionEffect();
```
