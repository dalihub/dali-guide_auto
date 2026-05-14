---
title: Ui Component Config
sidebar_label: Ui Component Config
category: views-components
---

# Ui Component Config

`UiComponentConfig` provides configuration for dali-ui-components default values. It extends `UiConfig` and serves as the primary configuration object for applications using the dali-ui-components library.

## Table of Contents

- [Creating and Applying Configuration](#creating-and-applying-configuration)
- [Inherited Configuration Methods](#inherited-configuration-methods)
- [Downcasting Handles](#downcasting-handles)

## Creating and Applying Configuration

Create a `UiComponentConfig` instance using `UiComponentConfig::New()` and apply it at application startup before entering the main loop. The configuration must be applied exactly once; after `Apply()` is called, the configuration is frozen and setter methods can no longer be used.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>
#include <dali/public-api/application/application.h>

int main(int argc, char** argv)
{
  Dali::Application application = Dali::Application::New(&argc, &argv);

  Dali::Ui::UiComponentConfig::New()
    .SetScalingFactor(1.5f)
    .SetDpi(320)
    .SetDefaultFontSize(24.0f)
    .Apply();

  // Application main loop
  application.MainLoop();
  return 0;
}
```

All setter methods return a reference to the object, enabling fluent method chaining as shown above.

## Inherited Configuration Methods

`UiComponentConfig` inherits all configuration methods from `UiConfig`. The following key setters are available before calling `Apply()`:

| Method | Description |
|---|---|
| `SetScalingFactor(float)` | Sets the scaling factor applied to spx and sdp units |
| `SetDpi(int)` | Sets the screen DPI for dp and sdp unit calculations |
| `SetBaselineDpi(int)` | Sets the baseline DPI used as reference for dp calculations |
| `SetKeyClickPolicy(KeyClickPolicy)` | Sets the default key click policy for clickable views |
| `SetExecutionKeyPredicate(ExecutionKeyPredicate)` | Sets the predicate for determining click execution keys |
| `SetKeyLongPressThreshold(uint32_t)` | Sets the threshold for recognizing key long-press |
| `SetTapRecognizerTime(uint32_t)` | Sets the tap recognizer time limit in milliseconds |
| `SetBrokenImageUrl(BrokenImageType, const Dali::String&)` | Sets the fallback image URL for load failures |
| `EnableFocusClearOnEscape(bool)` | Enables or disables focus clearing on Escape key |
| `SetDefaultInteractionEffect(Trait)` | Sets the default interaction effect for views |
| `SetDefaultFontSize(float)` | Sets the default font point-size for text elements |
| `SetDefaultTextColor(const Vector4&)` | Sets the default text color for text elements |

Additional methods for marquee configuration, placeholder text styling, focus indicator behavior, and async rendering are also available. See the `UiConfig` API reference for the complete list.

Example configuring multiple options:

```cpp
Dali::Ui::UiComponentConfig::New()
  .SetDpi(320)
  .SetBaselineDpi(160)
  .SetScalingFactor(1.0f)
  .SetDefaultFontSize(16.0f)
  .SetDefaultTextColor(Dali::Color::BLACK)
  .EnableFocusClearOnEscape(true)
  .Apply();
```

## Downcasting Handles

Use `UiComponentConfig::DownCast()` to safely convert a `BaseHandle` to a `UiComponentConfig` handle. If the handle does not point to a `UiComponentConfig`, the returned handle is left uninitialized.

```cpp
Dali::BaseHandle baseHandle = /* ... */;
Dali::Ui::UiComponentConfig config = Dali::Ui::UiComponentConfig::DownCast(baseHandle);

if (config)
{
  // Successfully downcast - config is valid
}
else
{
  // Handle was not a UiComponentConfig
}