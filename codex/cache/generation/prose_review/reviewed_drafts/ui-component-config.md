---
title: Ui Component Config
sidebar_label: Ui Component Config
category: styling-theme-config
---

# Ui Component Config

`Dali::Ui::UiComponentConfig` is the dali-ui configuration handle for default values used by dali-ui-components. It extends `Dali::Ui::UiConfig`, so application code can use the inherited configuration setters before applying the config.

## Table of Contents

- [Creating a Component Config](#creating-a-component-config)
- [Using Config Handles in Application Code](#using-config-handles-in-application-code)
- [Recovering a Typed Config Handle](#recovering-a-typed-config-handle)
- [Generated Chain Methods](#generated-chain-methods)

## Creating a Component Config

Create a component configuration object with `Dali::Ui::UiComponentConfig::New`. The returned `Dali::Ui::UiComponentConfig` is an initialized dali-ui handle with the `Dali::Ui::UiConfig` defaults inherited by the component config.

```cpp
Dali::Ui::UiComponentConfig config = Dali::Ui::UiComponentConfig::New();

if(config)
{
  // Store or pass the config through dali-ui application setup code.
}
```

For helper functions, prefer returning the typed handle directly instead of exposing a lower-level object model.

```cpp
Dali::Ui::UiComponentConfig CreateDefaultComponentConfig()
{
  Dali::Ui::UiComponentConfig config = Dali::Ui::UiComponentConfig::New();

  return config;
}
```

## Using Config Handles in Application Code

`Dali::Ui::UiComponentConfig` behaves like a DALi handle type. It can be copied by value where a shared handle is useful, and it supports move construction and move assignment through `Dali::Ui::UiComponentConfig::UiComponentConfig` and `Dali::Ui::UiComponentConfig::operator=`.

```cpp
Dali::Ui::UiComponentConfig MakeComponentConfig()
{
  return Dali::Ui::UiComponentConfig::New();
}

void PrepareComponentConfig()
{
  Dali::Ui::UiComponentConfig config = MakeComponentConfig();

  Dali::Ui::UiComponentConfig movedConfig = std::move(config);

  Dali::Ui::UiComponentConfig activeConfig;
  activeConfig = std::move(movedConfig);
}
```

This is useful when a setup function builds configuration state and returns or stores the handle for the application startup path.

```cpp
class ComponentConfigStore
{
public:
  void SetConfig(Dali::Ui::UiComponentConfig config)
  {
    mConfig = std::move(config);
  }

  Dali::Ui::UiComponentConfig GetConfig() const
  {
    return mConfig;
  }

private:
  Dali::Ui::UiComponentConfig mConfig;
};
```

## Recovering a Typed Config Handle

Use `Dali::Ui::UiComponentConfig::DownCast` when application infrastructure receives a generic DALi handle and needs to recover the typed config handle before using it as a `Dali::Ui::UiComponentConfig`.

```cpp
auto typedConfig = Dali::Ui::UiComponentConfig::DownCast(handle);

if(typedConfig)
{
  // typedConfig is a Dali::Ui::UiComponentConfig.
}
```

Keep the downcast at the boundary where the generic handle enters your dali-ui layer. After that point, pass `Dali::Ui::UiComponentConfig` through your application code as the typed value.

```cpp
void RegisterComponentConfig(Dali::Ui::UiComponentConfig config)
{
  if(!config)
  {
    return;
  }

  // Continue with typed dali-ui configuration flow.
}

void RegisterFromGenericHandle(Dali::BaseHandle handle)
{
  Dali::Ui::UiComponentConfig config = Dali::Ui::UiComponentConfig::DownCast(handle);
  RegisterComponentConfig(config);
}
```

## Generated Chain Methods

`ui-component-config.autogen.h` is part of the `UiComponentConfig` feature surface. It defines `DALI_UI_CHAIN_UICOMPONENTCONFIG_METHODS` as generated chain-method support for the `Dali::Ui::UiConfig` methods inherited by `Dali::Ui::UiComponentConfig`.

Application code normally works with the typed API on `Dali::Ui::UiComponentConfig` rather than invoking `DALI_UI_CHAIN_UICOMPONENTCONFIG_METHODS` directly. The generated methods support fluent setup style for inherited configuration setters such as `SetScalingFactor`, `SetDpi`, and related `Dali::Ui::UiConfig` configuration methods.

```cpp
Dali::Ui::UiComponentConfig config = Dali::Ui::UiComponentConfig::New();

// Generated chain methods, when present in the configured dali-ui build,
// are used on the typed Dali::Ui::UiComponentConfig handle.
if(config)
{
  // Keep configuration setup close to the View construction path.
}
```

For application code, the practical rule is simple: create the config with `Dali::Ui::UiComponentConfig::New`, keep it typed as `Dali::Ui::UiComponentConfig`, and use any generated chain methods exposed by the installed dali-ui headers as part of the same component configuration setup flow.
