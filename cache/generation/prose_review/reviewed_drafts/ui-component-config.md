---
title: Ui Component Config
sidebar_label: Ui Component Config
category: styling-theme-config
---

# Ui Component Config

`UiComponentConfig` provides global configuration for dali-ui components, controlling display scaling, DPI settings, text defaults, and interaction behaviors. It extends `UiConfig` and serves as the primary configuration object when using the `dali-ui-components` library.

## Table of Contents

- [Creating and Applying Configuration](#creating-and-applying-configuration)
- [Display Scaling and DPI](#display-scaling-and-dpi)
- [Text Defaults](#text-defaults)
- [Input and Interaction Settings](#input-and-interaction-settings)
- [Focus Behavior](#focus-behavior)
- [Broken Image Handling](#broken-image-handling)
- [Extending UiComponentConfig](#extending-uicomponentconfig)

## Creating and Applying Configuration

`UiComponentConfig` must be created and applied before the application main loop starts. After calling `Apply()`, the configuration becomes immutable and setter methods will trigger assertion failures.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

using namespace Dali::Ui;

int main(int argc, char** argv)
{
    Dali::Application application = Dali::Application::New(&argc, &argv);

    UiComponentConfig::New()
        .SetScalingFactor(1.5f)
        .SetDpi(320)
        .SetDefaultFontSize(18.0f)
        .Apply();

    // Application setup continues...
    application.MainLoop();
    return 0;
}
```

The `UiComponentConfig::New()` static method creates a new configuration handle with default values. All setter methods return a reference to the object, enabling fluent method chaining.

## Display Scaling and DPI

`UiComponentConfig` controls how density-independent pixels (dp) and scaled pixels (sdp) are calculated for the entire application.

### Scaling Factor

The scaling factor affects `spx` and `sdp` unit calculations. Use `SetScalingFactor()` to adjust the overall scale of UI elements.

```cpp
UiComponentConfig::New()
    .SetScalingFactor(1.5f)  // Scale all sdp values by 1.5x
    .Apply();
```

Retrieve the current scaling factor with `GetScalingFactor()`:

```cpp
UiComponentConfig config = UiComponentConfig::New();
float scale = config.GetScalingFactor();  // Returns 1.0f by default
```

### DPI Configuration

The DPI setting determines how density-independent pixels are converted to actual screen pixels. Set the screen DPI using `SetDpi()`:

```cpp
UiComponentConfig::New()
    .SetDpi(320)           // Screen DPI
    .SetBaselineDpi(160)   // Reference DPI (default)
    .Apply();
```

After calling `Apply()`, you can retrieve computed DPI factors:

```cpp
float dpiFactor = config.GetDpiFactor();       // dpi / baselineDpi
float scaledDpiFactor = config.GetScaledDpiFactor();  // dpiFactor * scalingFactor
```

## Text Defaults

`UiComponentConfig` provides global text settings that apply to all text-based components like `Label` and `InputField`.

### Font Size and Colors

```cpp
UiComponentConfig::New()
    .SetDefaultFontSize(16.0f)
    .SetDefaultTextColor(Dali::Vector4(0.0f, 0.0f, 0.0f, 1.0f))  // Black
    .SetDefaultPlaceholderTextColor(Dali::Vector4(0.7f, 0.7f, 0.7f, 1.0f))  // Gray
    .Apply();
```

Retrieve text defaults using getter methods:

```cpp
float fontSize = config.GetDefaultFontSize();
Dali::Vector4 textColor = config.GetDefaultTextColor();
Dali::Vector4 placeholderColor = config.GetDefaultPlaceholderTextColor();
```

### Placeholder Text Behavior

Control whether placeholder text remains visible when a control receives focus:

```cpp
UiComponentConfig::New()
    .SetShowPlaceholderTextOnFocus(true)  // Keep placeholder visible when focused
    .Apply();
```

### Marquee Settings

Configure text marquee animation defaults:

```cpp
UiComponentConfig::New()
    .SetMarqueeSpeed(100)              // Pixels per second
    .SetMarqueeLoopCount(3)            // Number of loops
    .SetMarqueeLoopDelay(1.0f)         // Delay between loops in seconds
    .SetMarqueeGap(50.0f)              // Gap before wrap in pixels
    .Apply();
```

### Asynchronous Text Rendering

Enable asynchronous text rendering for `Label` components by default:

```cpp
UiComponentConfig::New()
    .SetLabelAsyncRendering(true)
    .Apply();
```

## Input and Interaction Settings

### Key Click Policy

Configure how keyboard keys trigger click actions on interactive views:

```cpp
#include <dali-ui-foundation/public-api/key-click-policy.h>

UiComponentConfig::New()
    .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE)
    .Apply();
```

### Execution Key Predicate

Define which keys trigger click execution. The predicate must be a free function or stateless lambda:

```cpp
bool MyExecutionKeyPredicate(const Dali::String& keyName)
{
    return keyName == "Return" || keyName == "KP_Enter";
}

UiComponentConfig::New()
    .SetExecutionKeyPredicate(MyExecutionKeyPredicate)
    .Apply();
```

Pass `nullptr` to restore the default predicate (matches "Return" only).

### Key Long Press Threshold

Set the number of consecutive key press events required to recognize a long press:

```cpp
UiComponentConfig::New()
    .SetKeyLongPressThreshold(5)  // 5 consecutive presses for long press
    .Apply();
```

### Tap Recognition

Configure the tap recognizer time limit:

```cpp
UiComponentConfig::New()
    .SetTapRecognizerTime(500)  // 500 milliseconds
    .Apply();
```

### Default Interaction Effect

Set a default interaction effect applied to views when `AsInteractive()` is called:

```cpp
UiComponentConfig::New()
    .SetDefaultInteractionEffect(myInteractionEffectTrait)
    .Apply();
```

## Focus Behavior

### Focus Clear on Escape

Control whether focus is cleared when the Escape key is pressed:

```cpp
UiComponentConfig::New()
    .EnableFocusClearOnEscape(true)
    .Apply();
```

### Focus Indicator Visibility

Force the keyboard focus indicator to always be visible:

```cpp
UiComponentConfig::New()
    .SetAlwaysShowFocus(true)
    .Apply();
```

## Broken Image Handling

Configure fallback images displayed when image loading fails. Different image types allow size-appropriate fallbacks:

```cpp
UiComponentConfig::New()
    .SetBrokenImageUrl(UiConfig::BrokenImageType::SMALL, "images/broken-small.png")
    .SetBrokenImageUrl(UiConfig::BrokenImageType::NORMAL, "images/broken-normal.png")
    .SetBrokenImageUrl(UiConfig::BrokenImageType::LARGE, "images/broken-large.png")
    .Apply();
```

Retrieve broken image URLs:

```cpp
const Dali::String& smallUrl = config.GetBrokenImageUrl(UiConfig::BrokenImageType::SMALL);
const Dali::String& normalUrl = config.GetBrokenImageUrl(UiConfig::BrokenImageType::NORMAL);
const Dali::String& largeUrl = config.GetBrokenImageUrl(UiConfig::BrokenImageType::LARGE);
```

## Extending UiComponentConfig

Framework developers can subclass `UiComponentConfig` to provide pre-configured settings for specific platforms or devices:

```cpp
// TVConfig.h - Platform-specific configuration for TV devices
class TVConfig : public Dali::Ui::UiComponentConfig
{
public:
    static TVConfig New()
    {
        return UiComponentConfig::New()
            .SetDpi(72)
            .SetScalingFactor(2.0f)
            .SetDefaultFontSize(28.0f);
    }
};
```

Application developers can then use the platform configuration with a single line:

```cpp
TVConfig::New().Apply();
```

## Handle Operations

### DownCast

Convert a `BaseHandle` to a `UiComponentConfig` when the handle type is unknown:

```cpp
Dali::BaseHandle handle = GetSomeHandle();

if (Dali::Ui::UiComponentConfig config = Dali::Ui::UiComponentConfig::DownCast(handle))
{
    // Handle is a valid UiComponentConfig
    config.SetDpi(320);
}
```

`DownCast()` returns an uninitialized handle if the source handle is not a `UiComponentConfig`.
