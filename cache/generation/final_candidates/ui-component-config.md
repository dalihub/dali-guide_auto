---
title: Ui Component Config
sidebar_label: Ui Component Config
category: uncategorized
---

# Ui Component Config

`UiComponentConfig` provides configuration for dali-ui-components default values. It extends `UiConfig` and serves as the primary configuration object when using the dali-ui-components library.

## Table of Contents

- [Creating and Applying Configuration](#creating-and-applying-configuration)
- [Display and Scaling Settings](#display-and-scaling-settings)
- [Input and Focus Settings](#input-and-focus-settings)
- [Text and Label Settings](#text-and-label-settings)
- [Image Settings](#image-settings)
- [Type-Safe Handle Casting](#type-safe-handle-casting)

## Creating and Applying Configuration

`UiComponentConfig` must be created and applied before the application main loop starts. The configuration is immutable after `Apply()` is called.

### Basic Setup

Create a new configuration using `UiComponentConfig::New()` and apply it with fluent method chaining:

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

using namespace Dali::Ui;

int main(int argc, char** argv)
{
    Dali::Application application = Dali::Application::New(&argc, &argv);

    UiComponentConfig::New()
        .SetScalingFactor(1.5f)
        .SetDpi(320)
        .Apply();

    // ... application setup

    application.MainLoop();
    return 0;
}
```

### Configuration Lifecycle

The configuration follows an immutable-after-init pattern:

1. Create with `UiComponentConfig::New()`
2. Configure using setter methods (returns reference for chaining)
3. Call `Apply()` to freeze and activate the configuration
4. After `Apply()`, setter calls trigger assertion failures

```cpp
// Create and configure
UiComponentConfig config = UiComponentConfig::New();
config.SetScalingFactor(1.0f);
config.SetDpi(160);

// Apply to activate (freezes the config)
config.Apply();

// The following would trigger an assertion:
// config.SetDpi(320);  // Error: config is frozen
```

## Display and Scaling Settings

`UiComponentConfig` inherits display and scaling settings from `UiConfig`. These settings affect unit calculations (spx, dp, sdp) across the entire dali-ui framework.

### Scaling Factor

Use `SetScalingFactor()` to control the scaling applied to spx and sdp units:

```cpp
UiComponentConfig::New()
    .SetScalingFactor(1.5f)  // 150% scaling
    .Apply();
```

Retrieve the scaling factor with `GetScalingFactor()`:

```cpp
float scaling = config.GetScalingFactor();
```

### DPI Configuration

Configure screen DPI for dp and sdp unit calculations:

```cpp
UiComponentConfig::New()
    .SetDpi(320)           // Screen DPI
    .SetBaselineDpi(160)   // Reference DPI for dp calculations
    .Apply();
```

After applying, retrieve computed factors:

```cpp
float dpiFactor = config.GetDpiFactor();        // dpi / baselineDpi
float scaledDpiFactor = config.GetScaledDpiFactor();  // dpiFactor * scalingFactor
```

## Input and Focus Settings

Configure input behavior and focus management for interactive views.

### Key Click Policy

Set the default key click policy for clickable views:

```cpp
#include <dali-ui-foundation/public-api/key-click-policy.h>

UiComponentConfig::New()
    .SetKeyClickPolicy(KeyClickPolicy::ON_PRESS)
    .Apply();
```

### Execution Key Predicate

Define which keys trigger click execution:

```cpp
bool MyExecutionKeyPredicate(const Dali::String& keyName)
{
    return keyName == "Return" || keyName == "KP_Enter";
}

UiComponentConfig::New()
    .SetExecutionKeyPredicate(MyExecutionKeyPredicate)
    .Apply();
```

Pass `nullptr` to restore the default predicate (matches "Return" only):

```cpp
config.SetExecutionKeyPredicate(nullptr);
```

### Key Long Press Threshold

Configure the threshold for recognizing a key long-press:

```cpp
UiComponentConfig::New()
    .SetKeyLongPressThreshold(5)  // 5 consecutive key presses
    .Apply();
```

### Tap Recognition

Set the tap recognizer time limit:

```cpp
UiComponentConfig::New()
    .SetTapRecognizerTime(300)  // 300 milliseconds
    .Apply();
```

### Focus Behavior

Configure focus clearing and indicator visibility:

```cpp
UiComponentConfig::New()
    .EnableFocusClearOnEscape(true)
    .SetAlwaysShowFocus(true)
    .Apply();
```

### Default Interaction Effect

Set the default interaction effect applied to views:

```cpp
#include <dali-ui-foundation/public-api/trait.h>

UiComponentConfig::New()
    .SetDefaultInteractionEffect(myInteractionEffectTrait)
    .Apply();
```

## Text and Label Settings

Configure default text appearance and behavior.

### Font Size and Colors

Set default text styling:

```cpp
#include <dali/public-api/math/vector4.h>
#include <dali/public-api/common/color.h>

using namespace Dali;

UiComponentConfig::New()
    .SetDefaultFontSize(18.0f)
    .SetDefaultTextColor(Color::BLACK)
    .SetDefaultPlaceholderTextColor(Vector4(0.7f, 0.7f, 0.7f, 1.0f))
    .Apply();
```

### Placeholder Text Behavior

Control placeholder text visibility when focused:

```cpp
UiComponentConfig::New()
    .SetShowPlaceholderTextOnFocus(true)
    .Apply();
```

### Marquee Settings

Configure text marquee behavior:

```cpp
#include <dali-ui-foundation/public-api/text/text-enumerations.h>

using namespace Dali::Ui::Text;

UiComponentConfig::New()
    .SetMarqueeSpeed(100)                    // pixels per second
    .SetMarqueeLoopCount(3)                  // number of loops
    .SetMarqueeLoopDelay(0.5f)               // delay in seconds
    .SetMarqueeGap(50.0f)                    // gap in pixels
    .SetMarqueeStopMode(MarqueeStopMode::IMMEDIATE)
    .SetMarqueeOrientation(MarqueeOrientation::HORIZONTAL)
    .Apply();
```

### Label Async Rendering

Enable asynchronous text rendering for Label:

```cpp
UiComponentConfig::New()
    .SetLabelAsyncRendering(true)
    .Apply();
```

## Image Settings

Configure fallback images for loading failures.

### Broken Image URLs

Set different broken images based on view size:

```cpp
UiComponentConfig::New()
    .SetBrokenImageUrl(UiConfig::BrokenImageType::SMALL,  "broken_small.png")
    .SetBrokenImageUrl(UiConfig::BrokenImageType::NORMAL, "broken_normal.png")
    .SetBrokenImageUrl(UiConfig::BrokenImageType::LARGE,  "broken_large.png")
    .Apply();
```

Retrieve broken image URLs:

```cpp
const Dali::String& smallUrl = config.GetBrokenImageUrl(UiConfig::BrokenImageType::SMALL);
```

## Type-Safe Handle Casting

Use `DownCast()` to safely cast a base handle to `UiComponentConfig`:

```cpp
#include <dali/public-api/object/base-handle.h>

using namespace Dali;
using namespace Dali::Ui;

void ProcessHandle(BaseHandle handle)
{
    UiComponentConfig config = UiComponentConfig::DownCast(handle);

    if (config)
    {
        // Handle is a valid UiComponentConfig
        float scaling = config.GetScalingFactor();
    }
    else
    {
        // Handle is not a UiComponentConfig
    }
}
```

The `DownCast()` method returns an uninitialized handle if the source handle does not point to a `UiComponentConfig` object.
