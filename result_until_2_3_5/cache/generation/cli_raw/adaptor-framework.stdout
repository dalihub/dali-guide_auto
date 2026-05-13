---
title: Adaptor Framework
sidebar_label: Adaptor Framework
category: application-framework
---

# Adaptor Framework

The adaptor framework starts a DALi application, owns the platform lifecycle, and gives a dali-ui app the point where its `Dali::Ui::View` tree should be created.

## Table of Contents

- [Starting a dali-ui Application](#starting-a-dali-ui-application)
- [Handling Application Lifecycle Signals](#handling-application-lifecycle-signals)
- [Reacting to Device and Locale Changes](#reacting-to-device-and-locale-changes)
- [Rendering Without a Visible Window](#rendering-without-a-visible-window)

## Starting a dali-ui Application

Use `Dali::Application` as the normal entry point for a dali-ui app. Create the `Dali::Application` in `main()`, connect `InitSignal()`, and create your `Dali::Ui::View` hierarchy from that init callback. This keeps DALi object creation aligned with adaptor initialization.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
void CreateUi(Dali::Application application)
{
  auto window = application.GetWindow();

  Dali::Ui::View root =
    Dali::Ui::View::New()
      .SetRequestedWidth(Dali::Ui::MATCH_PARENT)
      .SetRequestedHeight(Dali::Ui::MATCH_PARENT)
      .SetBackgroundColor(Dali::Ui::UiColor(0x20242AFF));

  Dali::Ui::View panel =
    Dali::Ui::View::New()
      .SetRequestedWidth(360.0f)
      .SetRequestedHeight(240.0f)
      .SetRequestedPositionX(48.0f)
      .SetRequestedPositionY(48.0f)
      .SetBackgroundColor(Dali::Ui::UiColor(0xFFFFFFFF))
      .SetCornerRadius(18.0f);

  root.Add(panel);
  window.Add(root);
}
} // namespace

int DALI_EXPORT_API main(int argc, char** argv)
{
  Dali::Application application = Dali::Application::New(&argc, &argv);
  application.InitSignal().Connect(&CreateUi);
  application.MainLoop();
  return 0;
}
```

`Dali::Application::New()` accepts the process argument list. The adaptor recognizes DALi window and environment options such as width, height, DPI, and help, then updates `argc` and `argv` after consuming them.

## Handling Application Lifecycle Signals

`Dali::Application` exposes lifecycle signals for the common app states. Use `InitSignal()` to build the initial `Dali::Ui::View` tree, `PauseSignal()` and `ResumeSignal()` to stop and resume app activity, and `TerminateSignal()` to release application-owned resources.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
Dali::Ui::View gRoot;

void OnInit(Dali::Application application)
{
  auto window = application.GetWindow();

  gRoot =
    Dali::Ui::View::New()
      .SetRequestedWidth(Dali::Ui::MATCH_PARENT)
      .SetRequestedHeight(Dali::Ui::MATCH_PARENT)
      .SetBackgroundColor(Dali::Ui::UiColor(0x101820FF));

  window.Add(gRoot);
}

void OnPause(Dali::Application application)
{
  if(gRoot)
  {
    gRoot.SetOpacity(0.65f);
  }
}

void OnResume(Dali::Application application)
{
  if(gRoot)
  {
    gRoot.SetOpacity(1.0f);
  }
}

void OnTerminate(Dali::Application application)
{
  gRoot.Reset();
}
} // namespace

int DALI_EXPORT_API main(int argc, char** argv)
{
  Dali::Application application = Dali::Application::New(&argc, &argv);

  application.InitSignal().Connect(&OnInit);
  application.PauseSignal().Connect(&OnPause);
  application.ResumeSignal().Connect(&OnResume);
  application.TerminateSignal().Connect(&OnTerminate);

  application.MainLoop();
  return 0;
}
```

Call `Quit()` when the app should leave its main loop. Call `Lower()` when the app should move behind other applications without fully quitting.

```cpp
void RequestClose(Dali::Application application, bool keepWarm)
{
  if(keepWarm)
  {
    application.Lower();
  }
  else
  {
    application.Quit();
  }
}
```

## Reacting to Device and Locale Changes

The adaptor framework forwards device and locale changes through `Dali::Application`. Use `LanguageChangedSignal()` and `RegionChangedSignal()` when text, formatting, or content selection depends on the current locale. Use `LowBatterySignal()`, `LowMemorySignal()`, and `DeviceOrientationChangedSignal()` to adapt app behavior to device state.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
Dali::Ui::View gContent;

void OnInit(Dali::Application application)
{
  auto window = application.GetWindow();

  gContent =
    Dali::Ui::View::New()
      .SetRequestedWidth(Dali::Ui::MATCH_PARENT)
      .SetRequestedHeight(Dali::Ui::MATCH_PARENT)
      .SetBackgroundColor(Dali::Ui::UiColor(0xFFFFFFFF));

  window.Add(gContent);
}

void OnLanguageChanged(Dali::Application application)
{
  auto language = application.GetLanguage();
  auto region   = application.GetRegion();

  if(gContent)
  {
    gContent.InvalidateMeasure();
    gContent.InvalidateArrange();
  }
}

void OnLowBattery(Dali::DeviceStatus::Battery::Status status)
{
  if(status == Dali::DeviceStatus::Battery::CRITICALLY_LOW && gContent)
  {
    gContent.SetOpacity(0.8f);
  }
}

void OnLowMemory(Dali::DeviceStatus::Memory::Status status)
{
  if(status == Dali::DeviceStatus::Memory::CRITICALLY_LOW && gContent)
  {
    gContent.RemoveAllChildren();
  }
}

void OnOrientationChanged(Dali::DeviceStatus::Orientation::Status status)
{
  if(gContent)
  {
    gContent.InvalidateArrange();
  }
}
} // namespace

int DALI_EXPORT_API main(int argc, char** argv)
{
  Dali::Application application = Dali::Application::New(&argc, &argv);

  application.InitSignal().Connect(&OnInit);
  application.LanguageChangedSignal().Connect(&OnLanguageChanged);
  application.RegionChangedSignal().Connect(&OnLanguageChanged);
  application.LowBatterySignal().Connect(&OnLowBattery);
  application.LowMemorySignal().Connect(&OnLowMemory);
  application.DeviceOrientationChangedSignal().Connect(&OnOrientationChanged);

  application.MainLoop();
  return 0;
}
```

Use `Dali::Application::GetResourcePath()` for app resource lookup when assets are packaged with the application.

```cpp
void LoadApplicationResources()
{
  auto resourcePath = Dali::Application::GetResourcePath();
}
```

## Rendering Without a Visible Window

Use `Dali::OffscreenApplication` when the app needs DALi rendering without creating a visible native window. This is useful for background rendering, testing, or frame capture workflows. Unlike `Dali::Application`, `Dali::OffscreenApplication` does not run its own main loop; the application controls event processing and rendering.

```cpp
#include <dali/devel-api/adaptor-framework/offscreen-application.h>

int RenderOneOffscreenFrame(int argc, char** argv)
{
  Dali::OffscreenApplication application =
    Dali::OffscreenApplication::New(
      &argc,
      &argv,
      Dali::OffscreenApplication::FrameworkBackend::ECORE,
      Dali::OffscreenApplication::RenderMode::MANUAL);

  application.Start();

  auto offscreenWindow = application.GetWindow();

  application.RenderOnce();
  application.Terminate();

  return 0;
}
```

Choose `Dali::OffscreenApplication::RenderMode::AUTO` when rendering should be driven continuously by the surrounding event loop. Choose `Dali::OffscreenApplication::RenderMode::MANUAL` when the app should render only after an explicit `RenderOnce()` call.
