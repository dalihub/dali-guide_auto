---
title: Adaptor Framework
sidebar_label: Adaptor Framework
category: application-framework
---

# Adaptor Framework

The adaptor framework starts a DALi process, owns the application main loop, and delivers lifecycle and system events while your dali-ui content is built from `Dali::Ui::View`.

## Table of Contents

- [Starting a dali-ui Application](#starting-a-dali-ui-application)
- [Building the Initial View Tree](#building-the-initial-view-tree)
- [Handling Lifecycle Changes](#handling-lifecycle-changes)
- [Reacting to Device Status](#reacting-to-device-status)
- [Advanced Adaptor Ownership](#advanced-adaptor-ownership)

## Starting a dali-ui Application

Use `Dali::Application` as the normal entry point for a dali-ui app. Create it with `Dali::Application::New`, connect setup work to `InitSignal`, then enter `MainLoop`.

Create DALi and dali-ui objects after `InitSignal` is emitted. At that point, `GetWindow` returns the main `Dali::Window` that can host your root `Dali::Ui::View`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void OnInit(Application application)
{
  Window window = application.GetWindow();

  View root = View::New()
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(MATCH_PARENT)
    .SetBackgroundColor(UiColor(0.08f, 0.10f, 0.12f, 1.0f));

  window.Add(root);
}

int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);
  application.InitSignal().Connect(&OnInit);
  application.MainLoop();
  return 0;
}
```

`Dali::Application::New` accepts `argc` and `argv` by pointer because the adaptor may consume DALi command-line options before your application continues. `MainLoop` starts event processing and returns when the application exits.

## Building the Initial View Tree

In dali-ui applications, treat `Dali::Ui::View` as the app-facing object model. The adaptor provides the window and lifecycle; your UI content should be expressed as a `View` tree with typed setters and fluent chaining.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

View CreateContent()
{
  View header = View::New()
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(72.0f)
    .SetBackgroundColor(UiColor(0.12f, 0.22f, 0.35f, 1.0f));

  View body = View::New()
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(MATCH_PARENT)
    .SetBackgroundColor(UiColor(0.96f, 0.96f, 0.94f, 1.0f));

  View root = View::New()
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(MATCH_PARENT);

  root.Add(header);
  root.Add(body);

  return root;
}

void OnInit(Application application)
{
  application.GetWindow().Add(CreateContent());
}
```

`View::New` creates initialized view handles. `SetRequestedWidth`, `SetRequestedHeight`, and `SetBackgroundColor` are typed `View` setters, so the example avoids raw property writes. `Window::Add` attaches the root view to the main window; subsequent composition should happen through `View::Add` on parent views.

## Handling Lifecycle Changes

`Dali::Application` exposes lifecycle signals for app code that needs to pause work, resume work, or release resources. `PauseSignal`, `ResumeSignal`, `TerminateSignal`, `ResetSignal`, `LanguageChangedSignal`, and `RegionChangedSignal` use callbacks that receive a `Dali::Application`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;

void OnPause(Application application)
{
  // Pause app-owned work that should not run in the background.
}

void OnResume(Application application)
{
  // Resume app-owned work and refresh state if needed.
}

void OnTerminate(Application application)
{
  // Release app-owned resources before shutdown.
}

int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);

  application.PauseSignal().Connect(&OnPause);
  application.ResumeSignal().Connect(&OnResume);
  application.TerminateSignal().Connect(&OnTerminate);

  application.MainLoop();
  return 0;
}
```

For application-wide locale updates, use `GetLanguage` and `GetRegion` from the signal callback when rebuilding text or locale-sensitive content.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;

void OnLanguageChanged(Application application)
{
  Dali::String language = application.GetLanguage();
  Dali::String region = application.GetRegion();

  // Rebuild or refresh app text using language and region.
}

int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);
  application.LanguageChangedSignal().Connect(&OnLanguageChanged);
  application.RegionChangedSignal().Connect(&OnLanguageChanged);
  application.MainLoop();
  return 0;
}
```

## Reacting to Device Status

The adaptor framework also reports device-level status through `LowBatterySignal`, `LowMemorySignal`, and `DeviceOrientationChangedSignal`.

Use `Dali::DeviceStatus::Battery::Status`, `Dali::DeviceStatus::Memory::Status`, and `Dali::DeviceStatus::Orientation::Status` to branch on the delivered value.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;

void OnLowMemory(DeviceStatus::Memory::Status status)
{
  if(status == DeviceStatus::Memory::CRITICALLY_LOW)
  {
    // Drop optional cached data or defer expensive app work.
  }
}

void OnLowBattery(DeviceStatus::Battery::Status status)
{
  if(status == DeviceStatus::Battery::POWER_OFF)
  {
    // Save critical app state immediately.
  }
}

void OnDeviceOrientationChanged(DeviceStatus::Orientation::Status status)
{
  if(status == DeviceStatus::Orientation::ORIENTATION_90 ||
     status == DeviceStatus::Orientation::ORIENTATION_270)
  {
    // Update app layout state for a landscape device orientation.
  }
}

int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);

  application.LowMemorySignal().Connect(&OnLowMemory);
  application.LowBatterySignal().Connect(&OnLowBattery);
  application.DeviceOrientationChangedSignal().Connect(&OnDeviceOrientationChanged);

  application.MainLoop();
  return 0;
}
```

These callbacks are useful for app-owned resources and policy decisions. Keep the visible UI represented as `Dali::Ui::View` objects, and perform view updates through `View` APIs.

## Advanced Adaptor Ownership

Most dali-ui applications should use `Dali::Application`. `Dali::Adaptor` is the lower-level integration object for environments that provide their own platform main loop and window ownership.

With `Dali::Adaptor`, you create or receive a `Dali::Window`, create the adaptor with `Dali::Adaptor::New`, call `Start`, and then run your own platform loop. Once the adaptor is started, app code can attach a `Dali::Ui::View` tree to the window.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>
#include <dali/integration-api/adaptor-framework/adaptor.h>

using namespace Dali;
using namespace Dali::Ui;

void CreateUi(Window window)
{
  window.Add(
    View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT)
      .SetBackgroundColor(UiColor(0.18f, 0.18f, 0.20f, 1.0f)));
}

void StartWithExternalLoop(Window window)
{
  Adaptor& adaptor = Adaptor::New(window);
  adaptor.Start();

  CreateUi(window);

  // Run the platform event loop owned by the embedding environment.
}
```

Use this model only when your application already owns the platform loop. For normal dali-ui apps, `Dali::Application` remains the simpler and preferred application-facing entry point.
