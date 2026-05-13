---
title: UiComponentConfig
sidebar_label: UiComponentConfig
category: styling-theme-config
---

# UiComponentConfig

`Dali::Ui::UiComponentConfig` is the dali-ui-components configuration handle for component default values in a dali-ui application.

## Table of Contents

- [Create the component configuration handle](#create-the-component-configuration-handle)
- [Share configuration setup from application startup](#share-configuration-setup-from-application-startup)
- [Recover a typed component configuration handle](#recover-a-typed-component-configuration-handle)
- [Generated chaining support](#generated-chaining-support)

## Create the component configuration handle

Create a `Dali::Ui::UiComponentConfig` with `Dali::Ui::UiComponentConfig::New()`. The returned handle is initialized with the component configuration defaults inherited from `Dali::Ui::UiConfig`, such as `scalingFactor=1.0f`, `dpi=160`, `baselineDpi=160`, `defaultFontSize=16.0f`, and black default text color.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig CreateComponentConfig()
{
  return Dali::Ui::UiComponentConfig::New();
}
```

Use `Dali::Ui::UiComponentConfig` as application startup configuration when the application uses dali-ui-components. Apply it before building UI that depends on dali-ui units or component defaults; keep `Dali::Ui::View` as the app-facing object model for UI structure.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

void ConfigureDaliUiComponents()
{
  Dali::Ui::UiComponentConfig::New()
    .SetDpi(320)
    .SetScalingFactor(1.5f)
    .Apply();
}
```

## Share configuration setup from application startup

`Dali::Ui::UiComponentConfig` is a handle type. It supports normal handle lifetime, including destruction through `Dali::Ui::UiComponentConfig::~UiComponentConfig`, copy and move construction through `Dali::Ui::UiComponentConfig::UiComponentConfig`, and copy and move assignment through `Dali::Ui::UiComponentConfig::operator=`.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig BuildStartupComponentConfig()
{
  Dali::Ui::UiComponentConfig config =
    Dali::Ui::UiComponentConfig::New();

  return config;
}
```

When ownership should be transferred between startup helpers, move the handle explicitly before `Apply()` freezes the configuration values.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

#include <utility>

void ReplaceComponentConfig(Dali::Ui::UiComponentConfig& target)
{
  Dali::Ui::UiComponentConfig replacement =
    Dali::Ui::UiComponentConfig::New();

  target = std::move(replacement);
}
```

## Recover a typed component configuration handle

Use `Dali::Ui::UiComponentConfig::DownCast` when a handle is known only as `Dali::BaseHandle` and application code needs the typed `Dali::Ui::UiComponentConfig` handle again.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig RecoverComponentConfig(
  Dali::BaseHandle handle)
{
  return Dali::Ui::UiComponentConfig::DownCast(handle);
}
```

`Dali::Ui::UiComponentConfig::DownCast` returns a typed `Dali::Ui::UiComponentConfig` when the supplied handle refers to a component configuration object. If the supplied handle does not refer to that object type, the returned handle is uninitialized.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

void UseTypedComponentConfig(Dali::BaseHandle source)
{
  Dali::Ui::UiComponentConfig typed =
    Dali::Ui::UiComponentConfig::DownCast(source);

  (void)typed;
}
```

## Generated chaining support

`ui-component-config.autogen.h` is included by the public `Dali::Ui::UiComponentConfig` header. It defines `DALI_UI_CHAIN_UICOMPONENTCONFIG_METHODS`, and `Dali::Ui::UiComponentConfig` exposes generated fluent setters inherited from `Dali::Ui::UiConfig`, such as `SetDpi()` and `SetScalingFactor()`.

Application code normally includes `<dali-ui-components/public-api/ui-component-config.h>` and works with `Dali::Ui::UiComponentConfig::New()` directly. Configure values before calling `Apply()`; after `Apply()`, the configuration is frozen and setters must not be called.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig MakeConfigForViewSetup()
{
  return Dali::Ui::UiComponentConfig::New()
    .SetDpi(320)
    .SetScalingFactor(1.5f);
}
```
