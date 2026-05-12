---
title: Ui Config
sidebar_label: Ui Config
category: uncategorized
---

# Ui Config

`UiConfig` provides a centralized, immutable-after-init configuration object for dali-ui global settings. It controls display scaling, text defaults, focus behavior, marquee animation, and interaction effects across the entire application.

## Table of Contents

- [Basic Setup](#basic-setup)
- [Display Scaling and DPI](#display-scaling-and-dpi)
- [Text and Label Defaults](#text-and-label-defaults)
- [Marquee Configuration](#marquee-configuration)
- [Focus Behavior](#focus-behavior)
- [Input and Interaction](#input-and-interaction)
- [Broken Image Handling](#broken-image-handling)

## Basic Setup

`UiConfig` must be created and applied exactly once, typically in `main()` before the application main loop starts. After calling `Apply()`, the configuration becomes frozen and setter methods can no longer be used.

Create a new `UiConfig` using the static `UiConfig::New()` factory method, configure it using fluent method chaining, and apply it with `Apply()`:

```cpp
int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);

  UiConfig::New()
    .SetScalingFactor(1.5f)
    .SetDpi(320)
    .Apply();

  // Application code here
  application.MainLoop();
  return 0;
}
```

Calling `Apply()` more than once triggers an assertion failure. Unit literals such as `_spx`, `_dp`, and `_sdp` require `Apply()` to be called before they can be used.

## Display Scaling and DPI

`UiConfig` controls how density-independent pixels are calculated for different screen densities.

### Scaling Factor

The scaling factor is applied to `spx` and `sdp` unit calculations. Use `SetScalingFactor()` to configure it:

```cpp
UiConfig::New()
  .SetScalingFactor(1.5f)
  .Apply();
```

Retrieve the current scaling factor with `GetScalingFactor()`:

```cpp
float factor = uiConfig.GetScalingFactor();
```

### DPI Configuration

Set the screen DPI using `SetDpi()` for `dp` and `sdp` unit calculations:

```cpp
UiConfig::New()
  .SetDpi(320)  // High-density display
  .Apply();
```

The baseline DPI serves as the reference for `dp` calculations. Configure it with `SetBaselineDpi()`:

```cpp
UiConfig::New()
  .SetDpi(320)
  .SetBaselineDpi(160)  // Default baseline
  .Apply();
```

After applying the configuration, retrieve computed factors:

```cpp
float dpiFactor = uiConfig.GetDpiFactor();      // dpi / baselineDpi
float scaledDpiFactor = uiConfig.GetScaledDpiFactor();  // dpiFactor * scalingFactor
```

## Text and Label Defaults

`UiConfig` provides global defaults for text rendering and label behavior.

### Default Font Size and Colors

Set the default font size for text elements with `SetDefaultFontSize()`:

```cpp
UiConfig::New()
  .SetDefaultFontSize(16.0f)
  .Apply();
```

Configure default text and placeholder text colors:

```cpp
UiConfig::New()
  .SetDefaultTextColor(Vector4(0.2f, 0.2f, 0.2f, 1.0f))
  .SetDefaultPlaceholderTextColor(Vector4(0.5f, 0.5f, 0.5f, 1.0f))
  .Apply();
```

Retrieve default values:

```cpp
float fontSize = uiConfig.GetDefaultFontSize();
Vector4 textColor = uiConfig.GetDefaultTextColor();
Vector4 placeholderColor = uiConfig.GetDefaultPlaceholderTextColor();
```

### Asynchronous Label Rendering

Enable asynchronous text rendering for `Label` controls by default using `SetLabelAsyncRendering()`:

```cpp
UiConfig::New()
  .SetLabelAsyncRendering(true)
  .Apply();
```

Check whether async rendering is enabled:

```cpp
bool isAsync = uiConfig.IsLabelAsyncRendering();
```

### Placeholder Text on Focus

Control whether placeholder text remains visible when an input field has focus:

```cpp
UiConfig::New()
  .SetShowPlaceholderTextOnFocus(false)  // Hide placeholder when focused
  .Apply();
```

Query the current setting:

```cpp
bool shownOnFocus = uiConfig.IsPlaceholderTextShownOnFocus();
```

## Marquee Configuration

`UiConfig` provides global defaults for marquee animation behavior in `Label` controls.

### Speed and Looping

Configure marquee speed in pixels per second and loop count:

```cpp
UiConfig::New()
  .SetMarqueeSpeed(100)      // 100 pixels per second
  .SetMarqueeLoopCount(3)     // Loop 3 times
  .Apply();
```

Set the delay before marquee starts and between loops:

```cpp
UiConfig::New()
  .SetMarqueeLoopDelay(1.0f)  // 1 second delay
  .Apply();
```

Configure the gap before the marquee wraps:

```cpp
UiConfig::New()
  .SetMarqueeGap(50.0f)  // 50 pixel gap
  .Apply();
```

### Orientation and Stop Mode

Set the marquee scroll direction using `SetMarqueeOrientation()`:

```cpp
UiConfig::New()
  .SetMarqueeOrientation(Text::MarqueeOrientation::HORIZONTAL)
  .Apply();
```

Configure how the marquee stops using `SetMarqueeStopMode()`:

```cpp
UiConfig::New()
  .SetMarqueeStopMode(Text::MarqueeStopMode::IMMEDIATE)
  .Apply();
```

Retrieve current marquee settings:

```cpp
int speed = uiConfig.GetMarqueeSpeed();
int loopCount = uiConfig.GetMarqueeLoopCount();
float delay = uiConfig.GetMarqueeLoopDelay();
float gap = uiConfig.GetMarqueeGap();
Text::MarqueeOrientation orientation = uiConfig.GetMarqueeOrientation();
Text::MarqueeStopMode stopMode = uiConfig.GetMarqueeStopMode();
```

## Focus Behavior

`UiConfig` controls keyboard focus indicator visibility and focus clearing behavior.

### Focus Indicator Visibility

Configure whether the focus indicator is always shown:

```cpp
UiConfig::New()
  .SetAlwaysShowFocus(true)
  .Apply();
```

Check the current setting:

```cpp
bool alwaysShown = uiConfig.IsFocusIndicatorAlwaysShown();
```

### Focus Clear on Escape

Enable or disable automatic focus clearing when the Escape key is pressed:

```cpp
UiConfig::New()
  .EnableFocusClearOnEscape(true)
  .Apply();
```

Query the current setting:

```cpp
bool enabled = uiConfig.IsFocusClearOnEscapeEnabled();
```

## Input and Interaction

`UiConfig` provides settings for input event handling and interaction effects.

### Key Click Policy

Set the default key click policy for clickable views:

```cpp
UiConfig::New()
  .SetKeyClickPolicy(KeyClickPolicy::ON_PRESS)
  .Apply();
```

Retrieve the current policy:

```cpp
KeyClickPolicy policy = uiConfig.GetKeyClickPolicy();
```

### Execution Key Predicate

Define which keys trigger click execution on focused views. The predicate is a function pointer that receives the key name and returns `true` if the key should trigger a click:

```cpp
bool MyKeyPredicate(const Dali::String& keyName)
{
  return keyName == "Return" || keyName == "KP_Enter";
}

// In main():
UiConfig::New()
  .SetExecutionKeyPredicate(MyKeyPredicate)
  .Apply();
```

Pass `nullptr` to restore the default predicate that matches "Return" only.

### Key Long Press Threshold

Configure how many consecutive key press events are required to recognize a long-press:

```cpp
UiConfig::New()
  .SetKeyLongPressThreshold(5)  // 5 consecutive events
  .Apply();
```

Retrieve the threshold:

```cpp
uint32_t threshold = uiConfig.GetKeyLongPressThreshold();
```

### Tap Recognizer Time

Set the time limit for tap recognition in milliseconds:

```cpp
UiConfig::New()
  .SetTapRecognizerTime(300)  // 300ms
  .Apply();
```

Get the current time:

```cpp
uint32_t timeMs = uiConfig.GetTapRecognizerTime();
```

### Default Interaction Effect

Set the default interaction effect applied to views when `AsInteractive()` is called:

```cpp
UiConfig::New()
  .SetDefaultInteractionEffect(myInteractionEffect)
  .Apply();
```

Retrieve the default effect:

```cpp
Trait effect = uiConfig.GetDefaultInteractionEffect();
```

## Broken Image Handling

Configure fallback images to display when image loading fails. Different image types allow appropriate fallbacks based on view size.

### Setting Broken Images

Use `SetBrokenImageUrl()` with a `BrokenImageType` enum value:

```cpp
UiConfig::New()
  .SetBrokenImageUrl(UiConfig::BrokenImageType::SMALL, "images/broken-small.png")
  .SetBrokenImageUrl(UiConfig::BrokenImageType::NORMAL, "images/broken-normal.png")
  .SetBrokenImageUrl(UiConfig::BrokenImageType::LARGE, "images/broken-large.png")
  .Apply();
```

The `BrokenImageType` enum provides three sizes:

- `BrokenImageType::SMALL` - For small-sized views like icons
- `BrokenImageType::NORMAL` - For standard-sized views
- `BrokenImageType::LARGE` - For large image views

### Retrieving Broken Image URLs

Get the configured URL for a specific type:

```cpp
const Dali::String& url = uiConfig.GetBrokenImageUrl(UiConfig::BrokenImageType::NORMAL);
```
