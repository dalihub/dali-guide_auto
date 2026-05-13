---
title: Ui Scale Manager
sidebar_label: Ui Scale Manager
category: styling-theme-config
---

# Ui Scale Manager

`Dali::Ui::UiScaleManager` controls the application-wide UI scale used by dali-ui `Dali::Ui::View` layout trees.

## Table of Contents

- [Setting the Application UI Scale](#setting-the-application-ui-scale)
- [Reading and Reusing the Current Scale](#reading-and-reusing-the-current-scale)
- [Applying User or System Scale Presets](#applying-user-or-system-scale-presets)
- [Working with Scale Manager Handles](#working-with-scale-manager-handles)

## Setting the Application UI Scale

Use `Dali::Ui::UiScaleManager::Get` to access the process-wide scale manager, then call `Dali::Ui::UiScaleManager::SetScale` with the scale factor you want dali-ui layout roots to use.

The app-facing model is still your `Dali::Ui::View` tree. `Dali::Ui::UiScaleManager` is the central scale source that registered dali-ui layout roots use when recalculating layout.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ConfigureApplicationScale(float scale)
{
  Dali::Ui::UiScaleManager scaleManager = Dali::Ui::UiScaleManager::Get();
  scaleManager.SetScale(scale);
}
```

For one-off setup, calling `Dali::Ui::UiScaleManager::SetScale` directly on the singleton handle is concise:

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void UseLargeUiScale()
{
  Dali::Ui::UiScaleManager::Get().SetScale(1.5f);
}
```

`Dali::Ui::UiScaleManager::SetScale` ignores `NaN`, zero, and negative values. Pass a positive scale value for application behavior that should take effect.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyValidatedScale(float requestedScale)
{
  if(requestedScale > 0.0f)
  {
    Dali::Ui::UiScaleManager::Get().SetScale(requestedScale);
  }
}
```

## Reading and Reusing the Current Scale

Call `Dali::Ui::UiScaleManager::GetScale` when application logic needs to display, store, compare, or restore the current UI scale.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

float ReadApplicationScale()
{
  Dali::Ui::UiScaleManager scaleManager = Dali::Ui::UiScaleManager::Get();
  return scaleManager.GetScale();
}
```

A common pattern is to keep the previous value before temporarily applying another scale:

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

float ApplyTemporaryScale(float temporaryScale)
{
  Dali::Ui::UiScaleManager scaleManager = Dali::Ui::UiScaleManager::Get();

  float previousScale = scaleManager.GetScale();
  scaleManager.SetScale(temporaryScale);

  return previousScale;
}

void RestoreScale(float savedScale)
{
  Dali::Ui::UiScaleManager::Get().SetScale(savedScale);
}
```

Use `Dali::Ui::UiScaleManager::GetScale` instead of caching assumptions such as `1.0f`. The default scale is `1.0f`, but the current value may already have been changed by application or platform scale handling.

## Applying User or System Scale Presets

For dali-ui applications with a settings screen, accessibility preference, or test control, map the selected value to `Dali::Ui::UiScaleManager::SetScale`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

enum class UiScalePreset
{
  Compact,
  Normal,
  Comfortable,
  Large,
  ExtraLarge
};

float ToScaleFactor(UiScalePreset preset)
{
  switch(preset)
  {
    case UiScalePreset::Compact:
    {
      return 0.8f;
    }
    case UiScalePreset::Normal:
    {
      return 1.0f;
    }
    case UiScalePreset::Comfortable:
    {
      return 1.2f;
    }
    case UiScalePreset::Large:
    {
      return 1.5f;
    }
    case UiScalePreset::ExtraLarge:
    {
      return 2.0f;
    }
  }

  return 1.0f;
}

void ApplyScalePreset(UiScalePreset preset)
{
  Dali::Ui::UiScaleManager::Get().SetScale(ToScaleFactor(preset));
}
```

When a custom value comes from user input, validate it before passing it to `Dali::Ui::UiScaleManager::SetScale`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyCustomScale(float customScale)
{
  const bool withinApplicationRange = customScale >= 0.8f && customScale <= 2.0f;

  if(withinApplicationRange)
  {
    Dali::Ui::UiScaleManager::Get().SetScale(customScale);
  }
}
```

After a successful call to `Dali::Ui::UiScaleManager::SetScale`, dali-ui invalidates registered layout roots and schedules layout again. App code should normally update the scale manager, then let the `Dali::Ui::View` tree measure and arrange with the new effective scale.

## Working with Scale Manager Handles

`Dali::Ui::UiScaleManager` is a handle type. You can store the handle returned by `Dali::Ui::UiScaleManager::Get`, pass it through helper functions, or move it with `Dali::Ui::UiScaleManager::UiScaleManager` and `Dali::Ui::UiScaleManager::operator=`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

class ScaleController
{
public:
  ScaleController()
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

Use move construction or move assignment when transferring ownership of a stored handle between helper objects.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::UiScaleManager CreateScaleManagerHandle()
{
  return Dali::Ui::UiScaleManager::Get();
}

void ReplaceScaleManagerHandle()
{
  Dali::Ui::UiScaleManager scaleManager = CreateScaleManagerHandle();

  Dali::Ui::UiScaleManager replacement = Dali::Ui::UiScaleManager::Get();
  scaleManager = std::move(replacement);

  scaleManager.SetScale(1.2f);
}
```

`Dali::Ui::UiScaleManager::DownCast` is available when code receives a generic DALi handle and needs to recover a `Dali::Ui::UiScaleManager` handle.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::UiScaleManager RecoverScaleManager(Dali::BaseHandle handle)
{
  return Dali::Ui::UiScaleManager::DownCast(handle);
}
```

The `Dali::Ui::UiScaleManager::~UiScaleManager` destructor is the default handle destructor. Application code does not need explicit cleanup for the singleton scale manager handle.
