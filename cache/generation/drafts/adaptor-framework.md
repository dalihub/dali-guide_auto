---
title: Adaptor Framework
sidebar_label: Adaptor Framework
category: application-framework
---

# Adaptor Framework

The adaptor framework starts the DALi event loop, creates the main window, and delivers application lifecycle signals for a dali-ui `Dali::Ui::View` tree.

## Table of Contents

- [Starting a dali-ui Application](#starting-a-dali-ui-application)
- [Creating the Root View on Init](#creating-the-root-view-on-init)
- [Handling Application Lifecycle Signals](#handling-application-lifecycle-signals)
- [Using Runtime Application Context](#using-runtime-application-context)
- [Responding to System Pressure](#responding-to-system-pressure)

## Starting a dali-ui Application

Use `Dali::Application` as the app entry object. Create it with `Dali::Application::New`, connect startup work to `Dali::Application::InitSignal`, then enter `Dali::Application::MainLoop`.

`Dali::Application::InitSignal` uses the callback signature `void(Dali::Application)`, so the callback receives the initialized application handle.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
void OnInit(Dali::Application application)
{
  Dali::Ui::View root = Dali::Ui::View::New()
    .SetRequestedWidth(Dali::Ui::MATCH_PARENT)
    .SetRequestedHeight(Dali::Ui::MATCH_PARENT);

  application.GetWindow().Add(root);
}
} // namespace

int DALI_EXPORT_API main(int argc, char** argv)
{
  Dali::Application application = Dali::Application::New(&argc, &argv);
  application.InitSignal().Connect(&OnInit);
  application.MainLoop();

  return 0;
}
```

`Dali::Application::New` also accepts an optional stylesheet path:

```cpp
Dali::Application application =
  Dali::Application::New(&argc, &argv, "styles/app-theme.json");
```

## Creating the Root View on Init

Build the app-facing UI as a `Dali::Ui::View` hierarchy. The adaptor provides the main window through `Dali::Application::GetWindow`; the dali-ui content should remain expressed as `Dali::Ui::View` objects.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
Dali::Ui::View CreateContent()
{
  return Dali::Ui::View::New()
    .SetRequestedWidth(Dali::Ui::MATCH_PARENT)
    .SetRequestedHeight(Dali::Ui::MATCH_PARENT)
    .SetBackgroundColor(Dali::Ui::UiColor(0.08f, 0.09f, 0.11f, 1.0f))
    .Children({
      Dali::Ui::View::New()
        .SetRequestedWidth(240.0f)
        .SetRequestedHeight(120.0f)
        .SetBackgroundColor(Dali::Ui::UiColor::PRIMARY),

      Dali::Ui::View::New()
        .SetRequestedWidth(240.0f)
        .SetRequestedHeight(80.0f)
        .SetBackgroundColor(Dali::Ui::UiColor(0.20f, 0.32f, 0.70f, 1.0f))
    });
}

void OnInit(Dali::Application application)
{
  application.GetWindow().Add(CreateContent());
}
} // namespace
```

`Dali::Ui::View::New` creates an initialized view handle. `Dali::Ui::View::SetRequestedWidth` and `Dali::Ui::View::SetRequestedHeight` return the view, so they can be chained with `Dali::Ui::View::SetBackgroundColor` and `Dali::Ui::View::Children`.

## Handling Application Lifecycle Signals

Connect lifecycle-sensitive work to `Dali::Application` signals. `Dali::Application::PauseSignal`, `Dali::Application::ResumeSignal`, `Dali::Application::TerminateSignal`, `Dali::Application::ResetSignal`, `Dali::Application::LanguageChangedSignal`, and `Dali::Application::RegionChangedSignal` use the same `void(Dali::Application)` callback shape as `Dali::Application::InitSignal`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
void OnPause(Dali::Application application)
{
  (void)application;
}

void OnResume(Dali::Application application)
{
  (void)application;
}

void OnTerminate(Dali::Application application)
{
  (void)application;
}

void ConnectLifecycle(Dali::Application& application)
{
  application.PauseSignal().Connect(&OnPause);
  application.ResumeSignal().Connect(&OnResume);
  application.TerminateSignal().Connect(&OnTerminate);
}
} // namespace

int DALI_EXPORT_API main(int argc, char** argv)
{
  Dali::Application application = Dali::Application::New(&argc, &argv);
  ConnectLifecycle(application);
  application.MainLoop();

  return 0;
}
```

Call `Dali::Application::Quit` when the application should leave the main loop completely.

```cpp
void RequestShutdown(Dali::Application application)
{
  application.Quit();
}
```

## Using Runtime Application Context

`Dali::Application` exposes runtime context that is useful for locating resources and adapting content to the current environment.

Use `Dali::Application::GetResourcePath` for the application resource directory. Use `Dali::Application::GetLanguage` and `Dali::Application::GetRegion` when the app needs the current device language or region.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
void OnInit(Dali::Application application)
{
  Dali::String resourcePath = Dali::Application::GetResourcePath();
  Dali::String language     = application.GetLanguage();
  Dali::String region       = application.GetRegion();

  (void)resourcePath;
  (void)language;
  (void)region;

  Dali::Ui::View root = Dali::Ui::View::New()
    .SetRequestedWidth(Dali::Ui::MATCH_PARENT)
    .SetRequestedHeight(Dali::Ui::MATCH_PARENT);

  application.GetWindow().Add(root);
}
} // namespace
```

For updates while the app is already running, connect `Dali::Application::LanguageChangedSignal` or `Dali::Application::RegionChangedSignal` and refresh the affected `Dali::Ui::View` content from the callback.

```cpp
void OnLanguageChanged(Dali::Application application)
{
  Dali::String language = application.GetLanguage();
  (void)language;
}

void ConnectLocaleSignals(Dali::Application& application)
{
  application.LanguageChangedSignal().Connect(&OnLanguageChanged);
}
```

## Responding to System Pressure

The adaptor framework can notify an app about low battery and low memory conditions. `Dali::Application::LowBatterySignal` uses `Dali::DeviceStatus::Battery::Status`; `Dali::Application::LowMemorySignal` uses `Dali::DeviceStatus::Memory::Status`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
void OnLowBattery(Dali::DeviceStatus::Battery::Status status)
{
  (void)status;
}

void OnLowMemory(Dali::DeviceStatus::Memory::Status status)
{
  (void)status;
}

void ConnectSystemSignals(Dali::Application& application)
{
  application.LowBatterySignal().Connect(&OnLowBattery);
  application.LowMemorySignal().Connect(&OnLowMemory);
}
} // namespace
```

A typical dali-ui response is to reduce optional work and keep the existing `Dali::Ui::View` tree stable: defer nonessential loading, release app-owned caches, or postpone expensive content updates until `Dali::Application::ResumeSignal` or the next user-driven refresh.
