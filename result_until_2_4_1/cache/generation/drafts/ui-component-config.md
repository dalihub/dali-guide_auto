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

Create a `Dali::Ui::UiComponentConfig` with `Dali::Ui::UiComponentConfig::New()`. The returned handle is initialized with the default component configuration values provided by dali-ui-components.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig CreateComponentConfig()
{
  return Dali::Ui::UiComponentConfig::New();
}
```

Use `Dali::Ui::UiComponentConfig` as application startup configuration, separate from your `Dali::Ui::View` tree. The view tree remains the app-facing object model for UI structure, while `Dali::Ui::UiComponentConfig` represents component configuration state.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

void ConfigureDaliUiComponents()
{
  Dali::Ui::UiComponentConfig componentConfig =
    Dali::Ui::UiComponentConfig::New();

  (void)componentConfig;
}
```

## Share configuration setup from application startup

`Dali::Ui::UiComponentConfig` is a handle type. It supports normal handle lifetime, including destruction through `Dali::Ui::UiComponentConfig::~UiComponentConfig`, move construction through `Dali::Ui::UiComponentConfig::UiComponentConfig`, and move assignment through `Dali::Ui::UiComponentConfig::operator=`.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig BuildStartupComponentConfig()
{
  Dali::Ui::UiComponentConfig config =
    Dali::Ui::UiComponentConfig::New();

  return config;
}
```

When ownership should be transferred between startup helpers, move the handle explicitly.

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

Use `Dali::Ui::UiComponentConfig::DownCast` when a handle is known only through a base handle path and application code needs the typed `Dali::Ui::UiComponentConfig` handle again.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig RecoverComponentConfig(
  Dali::Ui::UiComponentConfig config)
{
  return Dali::Ui::UiComponentConfig::DownCast(config);
}
```

`Dali::Ui::UiComponentConfig::DownCast` returns a typed `Dali::Ui::UiComponentConfig` when the supplied handle refers to a component configuration object. If the supplied handle does not refer to that object type, the returned handle is uninitialized.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

void UseTypedComponentConfig(Dali::Ui::UiComponentConfig source)
{
  Dali::Ui::UiComponentConfig typed =
    Dali::Ui::UiComponentConfig::DownCast(source);

  (void)typed;
}
```

## Generated chaining support

`ui-component-config.autogen.h` is part of the public `Dali::Ui::UiComponentConfig` surface. It defines `DALI_UI_CHAIN_UICOMPONENTCONFIG_METHODS`, which expands the generated chain-method support for the component configuration type.

Application code normally includes `<dali-ui-components/public-api/ui-component-config.h>` and works with `Dali::Ui::UiComponentConfig::New()` directly. The generated header is included by the public component configuration header.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig MakeConfigForViewSetup()
{
  return Dali::Ui::UiComponentConfig::New();
}
```
