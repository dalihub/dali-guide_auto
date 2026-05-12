---
title: Addon Manager
sidebar_label: Addon Manager
category: platform-integration
---

# Addon Manager

Addon Manager lets a dali-ui application discover available DALi add-ons, load them by name, inspect their metadata, and call exported add-on procedures through typed function wrappers.

## Table of Contents

- [Using Addon Manager from a dali-ui app](#using-addon-manager-from-a-dali-ui-app)
- [Discovering add-ons and reading metadata](#discovering-add-ons-and-reading-metadata)
- [Loading add-ons and calling exported procedures](#loading-add-ons-and-calling-exported-procedures)
- [Registering an add-on dispatch table](#registering-an-add-on-dispatch-table)
- [Lifecycle callbacks](#lifecycle-callbacks)

## Using Addon Manager from a dali-ui app

In dali-ui application code, keep the visible object model centered on `Dali::Ui::View`. Use `Dali::Integration::AddOnManager` as a platform-integration service behind your view-level feature code rather than exposing add-on loading as part of the view tree.

`Dali::Integration::AddOnManager` is provided as a singleton created by the adaptor. Application code obtains it with `Dali::Integration::AddOnManager::Get()`. The function returns a pointer, so always check it before using the manager.

```cpp
#include <dali/integration-api/addon-manager.h>

void UseAddonManagerFromViewFeature()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return;
  }

  std::vector<std::string> addonNames = manager->EnumerateAddOns();

  for(const std::string& addonName : addonNames)
  {
    Dali::AddOnInfo info;
    if(manager->GetAddOnInfo(addonName, info))
    {
      const std::string& name = info.name;
      const uint32_t version = info.version;
      (void)name;
      (void)version;
    }
  }
}
```

`Dali::Integration::AddOnManager::EnumerateAddOns()` returns the available add-on names. `Dali::Integration::AddOnManager::GetAddOnInfo()` fills a `Dali::AddOnInfo` object for a named add-on and returns `true` when the metadata was retrieved.

## Discovering add-ons and reading metadata

Use `Dali::AddOnInfo` when you need to display, log, or filter add-on metadata before loading or invoking feature code. The public fields are `Dali::AddOnInfo::name`, `Dali::AddOnInfo::version`, `Dali::AddOnInfo::type`, `Dali::AddOnInfo::next`, and `Dali::AddOnInfo::buildInfo`.

`Dali::AddOnInfo::BuildInfo` stores the DALi library versions used to build the add-on. Its fields are `Dali::AddOnInfo::BuildInfo::libCoreVersion`, `Dali::AddOnInfo::BuildInfo::libAdaptorVersion`, and `Dali::AddOnInfo::BuildInfo::libToolkitVersion`.

```cpp
#include <dali/integration-api/addon-manager.h>

struct AddonMetadata
{
  std::string name;
  uint32_t version;
  uint32_t coreVersion;
  uint32_t adaptorVersion;
  uint32_t toolkitVersion;
};

std::vector<AddonMetadata> ReadAvailableAddonMetadata()
{
  std::vector<AddonMetadata> metadata;

  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return metadata;
  }

  const std::vector<std::string> addonNames = manager->EnumerateAddOns();

  for(const std::string& addonName : addonNames)
  {
    Dali::AddOnInfo info;
    if(manager->GetAddOnInfo(addonName, info))
    {
      metadata.push_back({
        info.name,
        info.version,
        info.buildInfo.libCoreVersion,
        info.buildInfo.libAdaptorVersion,
        info.buildInfo.libToolkitVersion
      });
    }
  }

  return metadata;
}
```

`Dali::AddOnInfo::next` is a raw extension-data pointer. Application code should only pass through or inspect data whose format is defined by the specific add-on contract.

## Loading add-ons and calling exported procedures

Use `Dali::Integration::AddOnManager::LoadAddOns()` when your application already knows a set of add-on names. It returns one handle per requested name. A null handle means that add-on was not loaded.

After loading, use the typed `Dali::Integration::AddOnManager::GetGlobalProc()` template to bind a named global procedure. The template argument must match the exported function signature.

```cpp
#include <dali/integration-api/addon-manager.h>

using IsSupportedProc = bool();

bool IsAddonSupported(const std::string& addonName)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return false;
  }

  std::vector<std::string> requestedAddons;
  requestedAddons.push_back(addonName);

  std::vector<void*> libraries = manager->LoadAddOns(requestedAddons);
  if(libraries.empty() || !libraries[0])
  {
    return false;
  }

  std::function<IsSupportedProc> isSupported =
    manager->GetGlobalProc<IsSupportedProc>(libraries[0], "IsSupported");

  if(!isSupported)
  {
    return false;
  }

  return isSupported();
}
```

For a single known add-on, `Dali::Integration::AddOnManager::GetAddOn()` is a convenience wrapper around `Dali::Integration::AddOnManager::LoadAddOns()`.

```cpp
#include <dali/integration-api/addon-manager.h>

using EnableProc = void(bool);

void EnableAddonFeature(const std::string& addonName, bool enabled)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return;
  }

  auto addon = manager->GetAddOn(addonName);
  if(!addon)
  {
    return;
  }

  std::function<EnableProc> enable =
    manager->GetGlobalProc<EnableProc>(addon, "Enable");

  if(enable)
  {
    enable(enabled);
  }
}
```

`Dali::Integration::AddOnManager::InvokeGlobalProc()` combines lookup and invocation. Use it only when the procedure is required by your add-on contract, because the call expects the named procedure to be available.

```cpp
#include <dali/integration-api/addon-manager.h>

int ReadAddonValue(const std::string& addonName)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return 0;
  }

  auto addon = manager->GetAddOn(addonName);
  if(!addon)
  {
    return 0;
  }

  return manager->InvokeGlobalProc<int>(addon, "GetValue");
}
```

Use `Dali::Integration::AddOnManager::LoadAddOn()` when the add-on name and library name are provided separately.

```cpp
#include <dali/integration-api/addon-manager.h>

using InitializeProc = bool();

bool LoadAddonFromLibrary(const std::string& addonName, const std::string& libraryName)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return false;
  }

  auto addon = manager->LoadAddOn(addonName, libraryName);
  if(!addon)
  {
    return false;
  }

  std::function<InitializeProc> initialize =
    manager->GetGlobalProc<InitializeProc>(addon, "Initialize");

  return initialize ? initialize() : false;
}
```

## Registering an add-on dispatch table

An add-on exposes itself to `Dali::Integration::AddOnManager` with `Dali::AddOnDispatchTable`. The table names the add-on and provides callbacks for metadata, global procedure lookup, instance procedure lookup, and lifecycle events.

The required table fields are `Dali::AddOnDispatchTable::name`, `Dali::AddOnDispatchTable::GetAddOnInfo`, `Dali::AddOnDispatchTable::GetGlobalProc`, and `Dali::AddOnDispatchTable::GetInstanceProc`. Lifecycle fields are `Dali::AddOnDispatchTable::OnStart`, `Dali::AddOnDispatchTable::OnResume`, `Dali::AddOnDispatchTable::OnPause`, and `Dali::AddOnDispatchTable::OnStop`.

```cpp
#include <dali/integration-api/addon-manager.h>

namespace
{
bool IsSupported()
{
  return true;
}

void FillAddonInfo(Dali::AddOnInfo& info)
{
  info.name = "example-addon";
  info.version = 1u;
  info.buildInfo.libCoreVersion = 0u;
  info.buildInfo.libAdaptorVersion = 0u;
  info.buildInfo.libToolkitVersion = 0u;
  info.next = nullptr;
}

void* LookupGlobalProc(const char* procName)
{
  const std::string name(procName);

  if(name == "IsSupported")
  {
    return reinterpret_cast<void*>(&IsSupported);
  }

  return nullptr;
}

void* LookupInstanceProc(const char* procName)
{
  (void)procName;
  return nullptr;
}

void OnStart()
{
}

void OnResume()
{
}

void OnPause()
{
}

void OnStop()
{
}

const Dali::AddOnDispatchTable EXAMPLE_ADDON_DISPATCH_TABLE =
{
  "example-addon",
  &FillAddonInfo,
  &LookupGlobalProc,
  &LookupInstanceProc,
  &OnStart,
  &OnResume,
  &OnPause,
  &OnStop
};
}

void RegisterExampleAddon()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(manager)
  {
    manager->RegisterAddOnDispatchTable(&EXAMPLE_ADDON_DISPATCH_TABLE);
  }
}
```

`Dali::Integration::AddOnManager::RegisterAddOnDispatchTable()` stores the table in the platform implementation. Once registered, application code can retrieve metadata with `Dali::Integration::AddOnManager::GetAddOnInfo()` and bind procedures with `Dali::Integration::AddOnManager::GetGlobalProc()` or `Dali::Integration::AddOnManager::GetInstanceProc()`.

## Lifecycle callbacks

The manager exposes lifecycle forwarding methods: `Dali::Integration::AddOnManager::Start()`, `Dali::Integration::AddOnManager::Resume()`, `Dali::Integration::AddOnManager::Pause()`, and `Dali::Integration::AddOnManager::Stop()`. These methods are intended for adaptor lifecycle forwarding. Add-ons participate by filling the matching callbacks in `Dali::AddOnDispatchTable`.

```cpp
#include <dali/integration-api/addon-manager.h>

namespace
{
void StartAddonServices()
{
}

void ResumeAddonServices()
{
}

void PauseAddonServices()
{
}

void StopAddonServices()
{
}

const Dali::AddOnDispatchTable LIFECYCLE_DISPATCH_TABLE =
{
  "lifecycle-addon",
  nullptr,
  nullptr,
  nullptr,
  &StartAddonServices,
  &ResumeAddonServices,
  &PauseAddonServices,
  &StopAddonServices
};
}

void RegisterLifecycleCallbacks()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(manager)
  {
    manager->RegisterAddOnDispatchTable(&LIFECYCLE_DISPATCH_TABLE);
  }
}
```

For a dali-ui application, view code normally reacts to application state through its usual app structure and `Dali::Ui::View` ownership. Add-on lifecycle callbacks are useful when the loaded native extension needs to start, pause, resume, or stop its own internal resources together with the DALi adaptor lifecycle.
