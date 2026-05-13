---
title: Addon Manager
sidebar_label: Addon Manager
category: platform-integration
---

# Addon Manager

Addon Manager lets a DALi application discover, inspect, load, and call platform-provided add-on libraries through `Dali::Integration::AddOnManager`.

## Table of Contents

- [Where Addon Manager Fits in a dali-ui App](#where-addon-manager-fits-in-a-dali-ui-app)
- [Discovering Available Add-ons](#discovering-available-add-ons)
- [Reading Add-on Metadata](#reading-add-on-metadata)
- [Loading an Add-on and Calling Global Procedures](#loading-an-add-on-and-calling-global-procedures)
- [Using Explicit Library Loading](#using-explicit-library-loading)
- [Lifecycle Forwarding](#lifecycle-forwarding)

## Where Addon Manager Fits in a dali-ui App

A dali-ui application should keep its user interface in the `Dali::Ui::View` tree and use Addon Manager only for platform integration work, such as loading optional functionality or resolving add-on procedures. The app-facing UI object model stays View-based; `Dali::Integration::AddOnManager` is a service object obtained from its singleton.

```cpp
#include <dali/integration-api/addon-manager.h>

void UseAddOnsFromViewCode()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return;
  }

  const std::vector<std::string> addOnNames = manager->EnumerateAddOns();
  for(const std::string& name : addOnNames)
  {
    // Use add-on availability to decide which dali-ui View behavior to enable.
  }
}
```

`Dali::Integration::AddOnManager::Get()` returns the manager created by the adaptor. If the platform or test environment has not installed an Addon Manager implementation, the returned pointer can be `nullptr`.

## Discovering Available Add-ons

Use `Dali::Integration::AddOnManager::EnumerateAddOns()` to ask the platform implementation for the available add-on names. The result is a `std::vector<std::string>` containing names that can be used with `Dali::Integration::AddOnManager::GetAddOnInfo()`, `Dali::Integration::AddOnManager::LoadAddOns()`, or `Dali::Integration::AddOnManager::GetAddOn()`.

```cpp
#include <dali/integration-api/addon-manager.h>

std::vector<std::string> GetAvailableAddOns()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return {};
  }

  return manager->EnumerateAddOns();
}
```

For a dali-ui app, discovery is usually done during feature setup. The UI can then expose only the actions backed by available add-ons, while the visual hierarchy remains based on `Dali::Ui::View`.

## Reading Add-on Metadata

`Dali::AddOnInfo` describes an add-on. `Dali::Integration::AddOnManager::GetAddOnInfo()` fills a caller-provided `Dali::AddOnInfo` and returns `true` when the information was retrieved.

The metadata fields are owned by `Dali::AddOnInfo`:

- `Dali::AddOnInfo::name`
- `Dali::AddOnInfo::type`
- `Dali::AddOnInfo::version`
- `Dali::AddOnInfo::next`
- `Dali::AddOnInfo::buildInfo`

Build version fields are grouped under `Dali::AddOnInfo::BuildInfo`:

- `Dali::AddOnInfo::BuildInfo::libCoreVersion`
- `Dali::AddOnInfo::BuildInfo::libAdaptorVersion`
- `Dali::AddOnInfo::BuildInfo::libToolkitVersion`

```cpp
#include <dali/integration-api/addon-manager.h>

bool ReadAddOnInfo(const std::string& addOnName, Dali::AddOnInfo& outInfo)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return false;
  }

  return manager->GetAddOnInfo(addOnName, outInfo);
}

void InspectAddOn(const std::string& addOnName)
{
  Dali::AddOnInfo info{};
  if(!ReadAddOnInfo(addOnName, info))
  {
    return;
  }

  const std::string name = info.name;
  const uint32_t version = info.version;
  const uint32_t coreVersion = info.buildInfo.libCoreVersion;
  const uint32_t adaptorVersion = info.buildInfo.libAdaptorVersion;
  const uint32_t toolkitVersion = info.buildInfo.libToolkitVersion;

  (void)name;
  (void)version;
  (void)coreVersion;
  (void)adaptorVersion;
  (void)toolkitVersion;
}
```

Use `Dali::AddOnInfo::next` only when an add-on contract documents additional data behind that pointer. For ordinary application checks, `Dali::AddOnInfo::name`, `Dali::AddOnInfo::version`, and `Dali::AddOnInfo::buildInfo` are the practical fields.

## Loading an Add-on and Calling Global Procedures

`Dali::Integration::AddOnManager::GetAddOn()` loads an add-on by name and returns an opaque handle. The handle is then used with `Dali::Integration::AddOnManager::GetGlobalProc()` or `Dali::Integration::AddOnManager::InvokeGlobalProc()`.

The typed `Dali::Integration::AddOnManager::GetGlobalProc()` overload returns a `std::function<T>`. The procedure name and signature must match the add-on contract.

```cpp
#include <dali/integration-api/addon-manager.h>

int CallAddOnSum()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return 0;
  }

  auto addOn = manager->GetAddOn("SampleAddOn");
  if(!addOn)
  {
    return 0;
  }

  std::function<int(int, int)> doSum = manager->GetGlobalProc<int(int, int)>(addOn, "DoSum");
  if(!doSum)
  {
    return 0;
  }

  return doSum(3, 4);
}
```

For a direct one-shot call, `Dali::Integration::AddOnManager::InvokeGlobalProc()` resolves and invokes the named global procedure with the requested return type.

```cpp
#include <dali/integration-api/addon-manager.h>

int InvokeAddOnSum()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return 0;
  }

  auto addOn = manager->GetAddOn("SampleAddOn");
  if(!addOn)
  {
    return 0;
  }

  return manager->InvokeGlobalProc<int>(addOn, "DoSum", 3, 4);
}
```

Use the typed `Dali::Integration::AddOnManager::GetGlobalProc()` form when the app will call the procedure repeatedly. Use `Dali::Integration::AddOnManager::InvokeGlobalProc()` when the call is isolated and the add-on contract is already known.

## Using Explicit Library Loading

`Dali::Integration::AddOnManager::LoadAddOn()` loads a named add-on from a specific library name. This is useful when the application or platform integration layer knows the library path or package-provided library name.

```cpp
#include <dali/integration-api/addon-manager.h>

bool LoadNamedAddOnFromLibrary(const std::string& addOnName, const std::string& libraryName)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return false;
  }

  auto addOn = manager->LoadAddOn(addOnName, libraryName);
  return addOn != nullptr;
}
```

For loading several add-ons by name, use `Dali::Integration::AddOnManager::LoadAddOns()`. The returned vector contains one opaque add-on handle for each requested name.

```cpp
#include <dali/integration-api/addon-manager.h>

std::vector<void*> LoadOptionalAddOns()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return {};
  }

  return manager->LoadAddOns({"SampleAddOn"});
}
```

After loading, use each non-null handle with `Dali::Integration::AddOnManager::GetGlobalProc()` or `Dali::Integration::AddOnManager::GetInstanceProc()` according to the add-on contract.

## Lifecycle Forwarding

`Dali::Integration::AddOnManager::Start()`, `Dali::Integration::AddOnManager::Resume()`, `Dali::Integration::AddOnManager::Pause()`, and `Dali::Integration::AddOnManager::Stop()` forward lifecycle events to loaded add-ons that registered corresponding callbacks through `Dali::AddOnDispatchTable`.

Applications normally let the adaptor own lifecycle forwarding. If your integration layer explicitly coordinates add-on lifecycle state, call these methods on the manager singleton.

```cpp
#include <dali/integration-api/addon-manager.h>

void ForwardAddOnLifecycle(bool visible)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return;
  }

  if(visible)
  {
    manager->Start();
    manager->Resume();
  }
  else
  {
    manager->Pause();
    manager->Stop();
  }
}
```

`Dali::AddOnDispatchTable` is the add-on-side registration contract. Its owned fields connect an add-on name, metadata callback, procedure lookup callbacks, and lifecycle callbacks:

```cpp
#include <dali/integration-api/addon-manager.h>

void RegisterDispatchTable(const Dali::AddOnDispatchTable* dispatchTable)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager || !dispatchTable)
  {
    return;
  }

  manager->RegisterAddOnDispatchTable(dispatchTable);
}
```

`Dali::AddOnDispatchTable::name` identifies the add-on. `Dali::AddOnDispatchTable::GetAddOnInfo`, `Dali::AddOnDispatchTable::GetGlobalProc`, and `Dali::AddOnDispatchTable::GetInstanceProc` provide metadata and function lookup. `Dali::AddOnDispatchTable::OnStart`, `Dali::AddOnDispatchTable::OnResume`, `Dali::AddOnDispatchTable::OnPause`, and `Dali::AddOnDispatchTable::OnStop` are optional lifecycle callbacks used when the manager forwards lifecycle changes.
