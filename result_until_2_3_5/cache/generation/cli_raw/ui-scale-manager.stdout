---
title: Ui Scale Manager
sidebar_label: Ui Scale Manager
category: styling-theme-config
---

# Ui Scale Manager

`Dali::Ui::UiScaleManager` provides the dali-ui application scale factor used by `Dali::Ui::View` layout roots.

## Table of Contents

- [Using the Shared Scale Manager](#using-the-shared-scale-manager)
- [Applying a UI Scale](#applying-a-ui-scale)
- [Reading the Active Scale](#reading-the-active-scale)
- [Handling Scale Values Safely](#handling-scale-values-safely)
- [Working with Handles](#working-with-handles)

## Using the Shared Scale Manager

Use `Dali::Ui::UiScaleManager::Get()` to access the process-wide scale manager. The returned `Dali::Ui::UiScaleManager` is a handle to the singleton manager, created on first use.

In a dali-ui application, treat this as the app-facing scale entry point for the `Dali::Ui::View` tree. Application code does not need to manage layout-root registration manually.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void InitializeUiScale()
{
  Dali::Ui::UiScaleManager scaleManager = Dali::Ui::UiScaleManager::Get();

  const float initialScale = scaleManager.GetScale();
  (void)initialScale;
}
```

`Dali::Ui::UiScaleManager::GetScale()` returns the current scale value. The default scale is `1.0f`.

## Applying a UI Scale

Call `Dali::Ui::UiScaleManager::SetScale()` when your application needs to apply a new system or user-selected UI scale. The scale is used by registered dali-ui layout roots, so `Dali::Ui::View` based layouts can be measured again with the updated effective scale.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyLargeUiScale()
{
  Dali::Ui::UiScaleManager scaleManager = Dali::Ui::UiScaleManager::Get();

  scaleManager.SetScale(1.5f);
}
```

For preset controls, keep the application logic small and route every choice through `Dali::Ui::UiScaleManager::SetScale()`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyPresetScale(float presetScale)
{
  Dali::Ui::UiScaleManager scaleManager = Dali::Ui::UiScaleManager::Get();

  scaleManager.SetScale(presetScale);
}

void UsePresetScales()
{
  ApplyPresetScale(0.8f);
  ApplyPresetScale(1.0f);
  ApplyPresetScale(1.2f);
  ApplyPresetScale(1.5f);
  ApplyPresetScale(2.0f);
}
```

## Reading the Active Scale

Use `Dali::Ui::UiScaleManager::GetScale()` when application UI needs to show or compare the active scale. Read from the manager instead of caching a separate application value, because `Dali::Ui::UiScaleManager` is the authoritative dali-ui scale source.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

float ReadCurrentUiScale()
{
  Dali::Ui::UiScaleManager scaleManager = Dali::Ui::UiScaleManager::Get();

  return scaleManager.GetScale();
}
```

A common pattern is to apply a scale, then read back the active value for display state owned by your app.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

float ApplyScaleAndReadBack(float scale)
{
  Dali::Ui::UiScaleManager scaleManager = Dali::Ui::UiScaleManager::Get();

  scaleManager.SetScale(scale);

  return scaleManager.GetScale();
}
```

## Handling Scale Values Safely

`Dali::Ui::UiScaleManager::SetScale()` accepts positive scale values. Invalid values such as zero, negative values, and `NaN` are ignored by the implementation.

Validate user-provided values before calling `Dali::Ui::UiScaleManager::SetScale()` so application behavior is explicit.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>
#include <cmath>

bool TrySetUiScale(float scale)
{
  if(std::isnan(scale) || scale <= 0.0f)
  {
    return false;
  }

  Dali::Ui::UiScaleManager scaleManager = Dali::Ui::UiScaleManager::Get();
  scaleManager.SetScale(scale);

  return true;
}
```

If the input is invalid, keep the current scale unchanged and continue using `Dali::Ui::UiScaleManager::GetScale()` as the current value.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>
#include <cmath>

float SetScaleOrKeepCurrent(float requestedScale)
{
  Dali::Ui::UiScaleManager scaleManager = Dali::Ui::UiScaleManager::Get();

  if(!std::isnan(requestedScale) && requestedScale > 0.0f)
  {
    scaleManager.SetScale(requestedScale);
  }

  return scaleManager.GetScale();
}
```

## Working with Handles

`Dali::Ui::UiScaleManager` is a DALi handle type. You can pass it by value, move it, and assign it like other DALi handles.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::UiScaleManager CreateScaleManagerHandle()
{
  return Dali::Ui::UiScaleManager::Get();
}

void StoreScaleManagerHandle()
{
  Dali::Ui::UiScaleManager scaleManager = CreateScaleManagerHandle();

  Dali::Ui::UiScaleManager movedScaleManager = std::move(scaleManager);
  movedScaleManager.SetScale(1.2f);
}
```

Use `Dali::Ui::UiScaleManager::DownCast()` only when you already have a generic `Dali::BaseHandle` and need to recover the typed manager handle.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

float ReadScaleFromBaseHandle(Dali::BaseHandle handle)
{
  Dali::Ui::UiScaleManager scaleManager = Dali::Ui::UiScaleManager::DownCast(handle);

  if(scaleManager)
  {
    return scaleManager.GetScale();
  }

  return 1.0f;
}
```

For normal application code, prefer `Dali::Ui::UiScaleManager::Get()` directly. Use `Dali::Ui::View` for your app-facing UI object model, and use `Dali::Ui::UiScaleManager` only for reading and applying the shared UI scale.
