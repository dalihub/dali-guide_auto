---
title: Ui Config
sidebar_label: Ui Config
category: application-framework
---

# Ui Config

`UiConfig` provides a centralized, immutable-after-init configuration object for dali-ui global settings. It holds display and scaling parameters that affect unit calculations (spx, dp, sdp) across the entire dali-ui framework.

## Table of Contents

- [Basic Setup and Configuration](#basic-setup-and-configuration)
- [Display Scaling and DPI Settings](#display-scaling-and-dpi-settings)
- [Text and Label Defaults](#text-and-label-defaults)
- [Marquee Animation Defaults](#marquee-animation-defaults)
- [Focus and Input Configuration](#focus-and-input-configuration)
- [Image Loading Fallbacks](#image-loading-fallbacks)

## Basic Setup and Configuration

`UiConfig` must be configured and applied exactly once, typically in `main()` before the application main loop starts. After `Apply()` is called, the configuration is frozen and setter methods can no longer be used.

Create a new `UiConfig` using `UiConfig::New()`, configure it using fluent method chaining, and apply it with `Apply()`:

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);

  UiConfig::New()
    .SetScalingFactor(1.5f)
    .SetDpi(320)
    .Apply();

  // Application controller initialization...
  application.MainLoop();

  return 0;
}
```

The `Apply()` method freezes the configuration and makes it the global dali-ui configuration. Unit literals (`_spx`, `_dp`, `_sdp`) require `Apply()` to be called before they can be used. Calling `Apply()` more than once triggers an assertion failure.

## Display Scaling and DPI Settings

`UiConfig` controls how dali-ui calculates device-independent units across the application.

### Scaling Factor

The scaling factor is applied to `spx` and `sdp` unit calculations. Use `SetScalingFactor()` to set a custom scale:

```cpp
UiConfig::New()
  .SetScalingFactor(1.2f)
  .Apply();
```

Retrieve the current scaling factor using `GetScalingFactor()`:

```cpp
float scale = config.GetScalingFactor();
```

### DPI Configuration

The screen DPI affects `dp` and `sdp` unit calculations. Set the target display DPI with `SetDpi()`:

```cpp
UiConfig::New()
  .SetDpi(320)  // High-density display
  .Apply();
```

The baseline DPI serves as the reference for `dp` calculations. By default, both DPI and baseline DPI are 160. Set a custom baseline with `SetBaselineDpi()`:

```cpp
UiConfig::New()
  .SetDpi(320)
  .SetBaselineDpi(160)
  .Apply();
```

After `Apply()`, you can retrieve computed factors:

- `GetDpiFactor()` returns `dpi / baselineDpi`
- `GetScaledDpiFactor()` returns `dpiFactor * scalingFactor`

```cpp
float dpiFactor = config.GetDpiFactor();        // e.g., 2.0 for 320 DPI with 160 baseline
float scaledFactor = config.GetScaledDpiFactor(); // e.g., 2.4 with 1.2 scaling factor
```

## Text and Label Defaults

`UiConfig` provides global defaults for text rendering that apply to all `Label` and text input controls.

### Default Font Size and Colors

Set the default font size (in points) with `SetDefaultFontSize()`:

```cpp
UiConfig::New()
  .SetDefaultFontSize(14.0f)
  .Apply();
```

Configure default text and placeholder text colors:

```cpp
UiConfig::New()
  .SetDefaultTextColor(Vector4(0.2f, 0.2f, 0.2f, 1.0f))
  .SetDefaultPlaceholderTextColor(Vector4(0.5f, 0.5f, 0.5f, 1.0f))
  .Apply();
```

### Asynchronous Label Rendering

By default, `Label` renders text synchronously. Enable asynchronous rendering globally with `SetLabelAsyncRendering()`:

```cpp
UiConfig::New()
  .SetLabelAsyncRendering(true)
  .Apply();
```

This improves performance for complex layouts by rendering text in a background thread. Check the current setting with `IsLabelAsyncRendering()`.

### Placeholder Text on Focus

Control whether placeholder text remains visible when an input field has focus:

```cpp
UiConfig::New()
  .SetShowPlaceholderTextOnFocus(false)  // Hide placeholder when focused
  .Apply();
```

Query this setting with `IsPlaceholderTextShownOnFocus()`.

## Marquee Animation Defaults

`UiConfig` provides global defaults for marquee (scrolling text) animations in `Label` controls.

### Speed and Looping

Configure marquee speed in pixels per second and the number of complete loops:

```cpp
UiConfig::New()
  .SetMarqueeSpeed(100)      // 100 pixels per second
  .SetMarqueeLoopCount(3)     // Run 3 complete loops
  .Apply();
```

Set the delay before the marquee starts and between loops:

```cpp
UiConfig::New()
  .SetMarqueeLoopDelay(1.0f)  // 1 second delay
  .Apply();
```

### Gap and Orientation

Configure the gap before the marquee wraps:

```cpp
UiConfig::New()
  .SetMarqueeGap(50.0f)  // 50 pixel gap
  .Apply();
```

Set the marquee orientation using `Text::MarqueeOrientation`:

```cpp
UiConfig::New()
  .SetMarqueeOrientation(Text::MarqueeOrientation::HORIZONTAL)
  .Apply();
```

Available orientations:
- `Text::MarqueeOrientation::HORIZONTAL` - Scrolls horizontally
- `Text::MarqueeOrientation::VERTICAL` - Scrolls vertically from bottom to top

### Stop Behavior

Configure how marquee stops using `Text::MarqueeStopMode`:

```cpp
UiConfig::New()
  .SetMarqueeStopMode(Text::MarqueeStopMode::FINISH_LOOP)
  .Apply();
```

Available stop modes:
- `Text::MarqueeStopMode::IMMEDIATE` - Stops immediately at current position
- `Text::MarqueeStopMode::FINISH_LOOP` - Completes the current loop before stopping

## Focus and Input Configuration

`UiConfig` provides settings for keyboard focus behavior and input event handling.

### Focus Indicator

Control whether the keyboard focus indicator is always visible:

```cpp
UiConfig::New()
  .SetAlwaysShowFocus(true)
  .Apply();
```

Check with `IsFocusIndicatorAlwaysShown()`.

### Focus Clear on Escape

Enable automatic focus clearing when the Escape key is pressed:

```cpp
UiConfig::New()
  .EnableFocusClearOnEscape(true)
  .Apply();
```

Query with `IsFocusClearOnEscapeEnabled()`.

### Key Click Policy

Set the default key click policy for clickable views:

```cpp
UiConfig::New()
  .SetKeyClickPolicy(KeyClickPolicy::ON_PRESS)
  .Apply();
```

### Execution Key Predicate

Define which keys trigger click execution on focused views. The predicate is a function pointer that receives the key name and returns `true` if it should trigger a click:

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

Pass `nullptr` to restore the default predicate (matches "Return" only).

### Key Long Press Threshold

Set the number of consecutive key press events required to recognize a long press:

```cpp
UiConfig::New()
  .SetKeyLongPressThreshold(10)  // 10 consecutive events
  .Apply();
```

### Tap Recognition

Configure the tap recognizer time limit in milliseconds:

```cpp
UiConfig::New()
  .SetTapRecognizerTime(300)  // 300ms
  .Apply();
```

### Default Interaction Effect

Set a default interaction effect applied to views when `AsInteractive()` is called:

```cpp
UiConfig::New()
  .SetDefaultInteractionEffect(myInteractionEffectTrait)
  .Apply();
```

Retrieve with `GetDefaultInteractionEffect()`.

## Image Loading Fallbacks

Configure fallback images displayed when image loading fails. Different broken image types allow appropriate fallbacks based on view size.

### Broken Image Types

The `BrokenImageType` enum defines three sizes:

- `BrokenImageType::SMALL` - For small icon-sized views
- `BrokenImageType::NORMAL` - For standard-sized views
- `BrokenImageType::LARGE` - For large image views

### Setting Broken Image URLs

Configure broken images for each type:

```cpp
UiConfig::New()
  .SetBrokenImageUrl(UiConfig::BrokenImageType::SMALL, "images/broken-small.png")
  .SetBrokenImageUrl(UiConfig::BrokenImageType::NORMAL, "images/broken-normal.png")
  .SetBrokenImageUrl(UiConfig::BrokenImageType::LARGE, "images/broken-large.png")
  .Apply();
```

Retrieve a broken image URL with `GetBrokenImageUrl()`:

```cpp
const Dali::String& url = config.GetBrokenImageUrl(UiConfig::BrokenImageType::NORMAL);
```
