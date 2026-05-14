---
title: Ui Scale Manager
sidebar_label: Ui Scale Manager
category: styling-theme-config
---

# Ui Scale Manager

`Dali::Ui::UiScaleManager` provides access to the shared UI scale used by dali-ui view layout.

## Table of Contents

- [Set the Application UI Scale](#set-the-application-ui-scale)
- [Read the Current Scale](#read-the-current-scale)
- [Work with the Manager Handle](#work-with-the-manager-handle)

## Set the Application UI Scale

Use `Dali::Ui::UiScaleManager::Get` to access the singleton scale manager, then call `Dali::Ui::UiScaleManager::SetScale` with the scale factor that should be used by dali-ui layout. In a dali-ui application, your app-facing object tree is made from `Dali::Ui::View` objects; layout roots are registered by the view implementation, so a scale change invalidates registered layout roots and causes the affected view tree to be measured again.

```cpp
#include <cmath>
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyUiScale(float requestedScale)
{
  if(requestedScale > 0.0f && std::isfinite(requestedScale))
  {
    Dali::Ui::UiScaleManager::Get().SetScale(requestedScale);
  }
}
```

A common pattern is to expose a small set of preset scale values from application settings or a developer menu:

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyCompactUiScale()
{
  Dali::Ui::UiScaleManager::Get().SetScale(0.8f);
}

void ApplyDefaultUiScale()
{
  Dali::Ui::UiScaleManager::Get().SetScale(1.0f);
}

void ApplyLargeUiScale()
{
  Dali::Ui::UiScaleManager::Get().SetScale(1.5f);
}
```

Pass a positive finite value to `Dali::Ui::UiScaleManager::SetScale`. The current implementation logs an error and keeps the existing scale when the value is `NaN` or non-positive.

## Read the Current Scale

Call `Dali::Ui::UiScaleManager::GetScale` when application logic needs to display, store, or compare the current UI scale. The default value is `1.0f`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>
#include <string>

std::string BuildScaleStatusText()
{
  const float scale = Dali::Ui::UiScaleManager::Get().GetScale();
  return "System Scale: " + std::to_string(scale);
}
```

When you update scale from user input, read the value back from `Dali::Ui::UiScaleManager::GetScale` after `Dali::Ui::UiScaleManager::SetScale` so that your UI reflects the value currently held by the manager.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

float ApplyAndReadUiScale(float scale)
{
  Dali::Ui::UiScaleManager manager = Dali::Ui::UiScaleManager::Get();

  manager.SetScale(scale);

  return manager.GetScale();
}
```

This keeps scale-related UI state separate from individual `Dali::Ui::View` objects. Views participate through the dali-ui layout system; application code normally updates the shared manager rather than applying separate layout scale values to each view.

## Work with the Manager Handle

`Dali::Ui::UiScaleManager` is a handle type. Store it when several functions in the same controller need to read or write the current scale, or fetch it locally with `Dali::Ui::UiScaleManager::Get` when the value is needed in one place.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

class UiScaleController
{
public:
  UiScaleController()
  : mScaleManager(Dali::Ui::UiScaleManager::Get())
  {
  }

  void SetScale(float scale)
  {
    mScaleManager.SetScale(scale);
  }

  float GetScale() const
  {
    return mScaleManager.GetScale();
  }

private:
  Dali::Ui::UiScaleManager mScaleManager;
};
```

Like other DALi handle types, `Dali::Ui::UiScaleManager` supports copy and move construction and assignment. Most application code only needs the handle returned by `Dali::Ui::UiScaleManager::Get`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>
#include <utility>

Dali::Ui::UiScaleManager TakeScaleManager()
{
  Dali::Ui::UiScaleManager manager = Dali::Ui::UiScaleManager::Get();
  return manager;
}

void ReplaceScaleManager(Dali::Ui::UiScaleManager& target)
{
  Dali::Ui::UiScaleManager replacement = Dali::Ui::UiScaleManager::Get();
  target = std::move(replacement);
}
```

Use `Dali::Ui::UiScaleManager::DownCast` only when you are handed a generic DALi handle and need to recover the scale manager handle.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

float ReadScaleFromHandle(Dali::BaseHandle handle)
{
  Dali::Ui::UiScaleManager manager = Dali::Ui::UiScaleManager::DownCast(handle);

  if(manager)
  {
    return manager.GetScale();
  }

  return Dali::Ui::UiScaleManager::Get().GetScale();
}
```
