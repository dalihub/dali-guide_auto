---
title: Ui Component Config
sidebar_label: Ui Component Config
category: styling-theme-config
---

# Ui Component Config

`Dali::Ui::UiComponentConfig` is the dali-ui component configuration handle used during app startup before building the `Dali::Ui::View` hierarchy.

## Table of Contents

- [Create the Component Configuration](#create-the-component-configuration)
- [Pass Configuration Through App Startup Code](#pass-configuration-through-app-startup-code)
- [Recover a Typed Configuration Handle](#recover-a-typed-configuration-handle)
- [Generated Chain Support](#generated-chain-support)

## Create the Component Configuration

Use `Dali::Ui::UiComponentConfig::New()` to create an initialized `Dali::Ui::UiComponentConfig` handle. In a dali-ui application, keep this setup near the point where your app initializes its UI configuration, before constructing the main `Dali::Ui::View` tree.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig CreateUiComponentConfig()
{
  Dali::Ui::UiComponentConfig config = Dali::Ui::UiComponentConfig::New();
  return config;
}
```

`Dali::Ui::UiComponentConfig` is a handle type. Returning it by value is the normal usage style for small setup helpers.

## Pass Configuration Through App Startup Code

`Dali::Ui::UiComponentConfig` supports move construction and move assignment. This is useful when your startup code creates the configuration in one helper and stores it in another object that owns the app setup flow.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>
#include <utility>

class UiStartupConfig
{
public:
  explicit UiStartupConfig(Dali::Ui::UiComponentConfig config)
  : componentConfig(std::move(config))
  {
  }

  void Replace(Dali::Ui::UiComponentConfig config)
  {
    componentConfig = std::move(config);
  }

private:
  Dali::Ui::UiComponentConfig componentConfig;
};

UiStartupConfig MakeStartupConfig()
{
  Dali::Ui::UiComponentConfig config = Dali::Ui::UiComponentConfig::New();
  return UiStartupConfig(std::move(config));
}
```

The move constructor `Dali::Ui::UiComponentConfig::UiComponentConfig(UiComponentConfig&&)` and move assignment operator `Dali::Ui::UiComponentConfig::operator=` preserve the typed handle while avoiding unnecessary copies in setup code.

## Recover a Typed Configuration Handle

Use `Dali::Ui::UiComponentConfig::DownCast()` when a configuration handle has passed through generic handle-based code and you need to recover the component-specific type.

```cpp
#include <dali/public-api/object/base-handle.h>
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig AsComponentConfig(Dali::BaseHandle handle)
{
  return Dali::Ui::UiComponentConfig::DownCast(handle);
}
```

`Dali::Ui::UiComponentConfig::DownCast()` returns a `Dali::Ui::UiComponentConfig`. Use this pattern at module boundaries where setup code stores or forwards handles generically, then restores the dali-ui component configuration type before continuing.

## Generated Chain Support

`ui-component-config.autogen.h` is part of the public feature surface for `Dali::Ui::UiComponentConfig`. It provides `DALI_UI_CHAIN_UICOMPONENTCONFIG_METHODS`, which expands the generated chain-method support for the component configuration class.

Application code normally includes the public feature header:

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig BuildComponentConfig()
{
  return Dali::Ui::UiComponentConfig::New();
}
```

The generated header is included by the public `Dali::Ui::UiComponentConfig` header. App code should rely on `Dali::Ui::UiComponentConfig` and `Dali::Ui::UiComponentConfig::New()` directly, then use the resulting configuration during dali-ui startup before creating the app-facing `Dali::Ui::View` objects.
