---
title: Addon Manager
sidebar_label: Addon Manager
category: platform-integration
---

# Addon Manager

Addon Manager lets a DALi application discover available add-ons, read their metadata, load them, and call functions exposed by those add-ons.

## Table of Contents

- [Accessing the Add-on Manager](#accessing-the-add-on-manager)
- [Discovering Add-ons and Reading Metadata](#discovering-add-ons-and-reading-metadata)
- [Loading Add-ons and Calling Global Functions](#loading-add-ons-and-calling-global-functions)
- [Calling Instance Functions](#calling-instance-functions)
- [Loading a Specific Library](#loading-a-specific-library)
- [Lifecycle Forwarding](#lifecycle-forwarding)
- [Registering a Dispatch Table](#registering-a-dispatch-table)

## Accessing the Add-on Manager

`Dali::Integration::AddOnManager` is created by the DALi adaptor and exposed as a singleton. Application code obtains it with `Dali::Integration::AddOnManager::Get()` and should check the returned pointer before using it.

```cpp
#include <dali/integration-api/addon-manager.h>

void UseAddOnManager()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return;
  }

  std::vector<std::string> addOnNames = manager->EnumerateAddOns();
}
```

Application code should not create a `Dali::Integration::AddOnManager` directly. The constructor is protected, the manager instance is created by the adaptor, and `Dali::Integration::AddOnManager::Get()` returns the currently installed singleton.

## Discovering Add-ons and Reading Metadata

Use `Dali::Integration::AddOnManager::EnumerateAddOns()` to get the available add-on names. For each name, call `Dali::Integration::AddOnManager::GetAddOnInfo()` to fill a `Dali::AddOnInfo` structure.

`Dali::AddOnInfo` describes the add-on through fields such as `Dali::AddOnInfo::name`, `Dali::AddOnInfo::version`, `Dali::AddOnInfo::type`, `Dali::AddOnInfo::next`, and `Dali::AddOnInfo::buildInfo`. The nested `Dali::AddOnInfo::BuildInfo` stores build-version values in `Dali::AddOnInfo::BuildInfo::libCoreVersion`, `Dali::AddOnInfo::BuildInfo::libAdaptorVersion`, and `Dali::AddOnInfo::BuildInfo::libToolkitVersion`.

```cpp
#include <dali/integration-api/addon-manager.h>

void PrintAvailableAddOns()
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return;
  }

  const std::vector<std::string> names = manager->EnumerateAddOns();

  for(const std::string& name : names)
  {
    Dali::AddOnInfo info{};
    if(manager->GetAddOnInfo(name, info))
    {
      const std::string addOnName = info.name;
      const uint32_t addOnVersion = info.version;
      const uint32_t coreVersion = info.buildInfo.libCoreVersion;
      const uint32_t adaptorVersion = info.buildInfo.libAdaptorVersion;
      const uint32_t toolkitVersion = info.buildInfo.libToolkitVersion;

      (void)addOnName;
      (void)addOnVersion;
      (void)coreVersion;
      (void)adaptorVersion;
      (void)toolkitVersion;
    }
  }
}
```

`Dali::Integration::AddOnManager::GetAddOnInfo()` returns `true` when the metadata was retrieved. Use that result before trusting the contents of `Dali::AddOnInfo`.

## Loading Add-ons and Calling Global Functions

After choosing an add-on name, call `Dali::Integration::AddOnManager::GetAddOn()` or `Dali::Integration::AddOnManager::LoadAddOns()` to obtain an opaque add-on handle. Then use the typed template overload of `Dali::Integration::AddOnManager::GetGlobalProc()` to retrieve a callable `std::function`.

```cpp
#include <dali/integration-api/addon-manager.h>

int RunAddOnSum(const std::string& addOnName)
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return 0;
  }

  auto addOn = manager->GetAddOn(addOnName);
  if(!addOn)
  {
    return 0;
  }

  auto doSum = manager->GetGlobalProc<int(int, int)>(addOn, "DoSum");
  if(!doSum)
  {
    return 0;
  }

  return doSum(3, 4);
}
```

For multiple add-ons, pass the selected names to `Dali::Integration::AddOnManager::LoadAddOns()`.

```cpp
#include <dali/integration-api/addon-manager.h>

void LoadOptionalAddOns()
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return;
  }

  const std::vector<std::string> names =
  {
    "SampleAddOn",
    "ImagePipelineAddOn"
  };

  auto addOns = manager->LoadAddOns(names);

  for(auto addOn : addOns)
  {
    if(!addOn)
    {
      continue;
    }

    auto initialize = manager->GetGlobalProc<void()>(addOn, "Initialize");
    if(initialize)
    {
      initialize();
    }
  }
}
```

When the function is required and you already know it exists, `Dali::Integration::AddOnManager::InvokeGlobalProc()` can retrieve and invoke it in one expression from an existing add-on handle.

```cpp
#include <dali/integration-api/addon-manager.h>

int InvokeRequiredFunction(const std::string& addOnName)
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return 0;
  }

  auto addOn = manager->GetAddOn(addOnName);
  if(!addOn)
  {
    return 0;
  }

  return manager->InvokeGlobalProc<int>(addOn, "DoSum", 10, 20);
}
```

Use `Dali::Integration::AddOnManager::GetGlobalProc()` when the function may be absent, because it lets the app check the returned callable before invoking it.

## Calling Instance Functions

Some add-ons expose a global creation function and separate instance functions. Retrieve the creation function with `Dali::Integration::AddOnManager::GetGlobalProc()`, then retrieve the instance entry point with `Dali::Integration::AddOnManager::GetInstanceProc()`.

```cpp
#include <dali/integration-api/addon-manager.h>

uint32_t CallAddOnInstance(const std::string& addOnName)
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return 0u;
  }

  auto addOn = manager->GetAddOn(addOnName);
  if(!addOn)
  {
    return 0u;
  }

  auto createInstance = manager->GetGlobalProc<void*()>(addOn, "CreateInstance");
  auto instanceCall = manager->GetInstanceProc<uint32_t(void*)>(addOn, "InstanceCall");

  if(!createInstance || !instanceCall)
  {
    return 0u;
  }

  void* instance = createInstance();
  if(!instance)
  {
    return 0u;
  }

  return instanceCall(instance);
}
```

The template argument passed to `Dali::Integration::AddOnManager::GetInstanceProc()` must match the exported function signature. For example, `uint32_t(void*)` means the add-on function returns `uint32_t` and receives one `void*` instance pointer.

## Loading a Specific Library

Use `Dali::Integration::AddOnManager::LoadAddOn()` when the application has both the logical add-on name and the library name. The method returns a handle when the add-on can be loaded, otherwise it returns a null handle.

```cpp
#include <dali/integration-api/addon-manager.h>

bool LoadNamedLibrary()
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return false;
  }

  auto addOn = manager->LoadAddOn("SampleAddOn", "libSampleAddOn.so");
  if(!addOn)
  {
    return false;
  }

  auto stringLength = manager->GetGlobalProc<int(const char*)>(addOn, "StringLen");
  if(!stringLength)
  {
    return false;
  }

  return stringLength("dali-ui") == 7;
}
```

`Dali::Integration::AddOnManager::GetAddOn()` is the shorter path when the manager already knows how to locate an add-on by name. `Dali::Integration::AddOnManager::LoadAddOn()` is useful when the caller must provide the library name explicitly.

## Lifecycle Forwarding

`Dali::Integration::AddOnManager::Start()`, `Dali::Integration::AddOnManager::Resume()`, `Dali::Integration::AddOnManager::Pause()`, and `Dali::Integration::AddOnManager::Stop()` forward lifecycle events through add-on lifecycle callbacks. In a normal DALi application, the adaptor owns lifecycle integration. These methods are mainly useful for platform-integration code, test harnesses, or embedded hosts that explicitly coordinate add-on lifecycle.

```cpp
#include <dali/integration-api/addon-manager.h>

void ForwardHostLifecycle(bool visible)
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return;
  }

  manager->Start();

  if(visible)
  {
    manager->Resume();
  }
  else
  {
    manager->Pause();
  }

  manager->Stop();
}
```

Only call these lifecycle methods from code that owns the lifecycle boundary. Feature code inside a normal view-based dali-ui screen should use the add-on functions it needs and leave process lifecycle forwarding to the application or adaptor layer.

## Registering a Dispatch Table

`Dali::AddOnDispatchTable` is the bridge an add-on uses to expose its metadata, global functions, instance functions, and lifecycle callbacks to `Dali::Integration::AddOnManager`. The table contains the add-on-facing function pointers: `Dali::AddOnDispatchTable::name`, `Dali::AddOnDispatchTable::GetAddOnInfo`, `Dali::AddOnDispatchTable::GetGlobalProc`, `Dali::AddOnDispatchTable::GetInstanceProc`, `Dali::AddOnDispatchTable::OnStart`, `Dali::AddOnDispatchTable::OnResume`, `Dali::AddOnDispatchTable::OnPause`, and `Dali::AddOnDispatchTable::OnStop`.

```cpp
#include <dali/integration-api/addon-manager.h>

#include <string>

int DoSum(int lhs, int rhs)
{
  return lhs + rhs;
}

void FillAddOnInfo(Dali::AddOnInfo& info)
{
  info = Dali::AddOnInfo{};
  info.name = "SampleAddOn";
  info.version = 0x01000000u;
  info.next = nullptr;
  info.buildInfo.libCoreVersion = 0u;
  info.buildInfo.libAdaptorVersion = 0u;
  info.buildInfo.libToolkitVersion = 0u;
}

void* FindGlobalProc(const char* procName)
{
  if(procName && std::string(procName) == "DoSum")
  {
    return reinterpret_cast<void*>(&DoSum);
  }

  return nullptr;
}

void* FindInstanceProc(const char* procName)
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

void RegisterSampleAddOn()
{
  Dali::AddOnDispatchTable table{};
  table.name = "SampleAddOn";
  table.GetAddOnInfo = &FillAddOnInfo;
  table.GetGlobalProc = &FindGlobalProc;
  table.GetInstanceProc = &FindInstanceProc;
  table.OnStart = &OnStart;
  table.OnResume = &OnResume;
  table.OnPause = &OnPause;
  table.OnStop = &OnStop;

  auto* manager = Dali::Integration::AddOnManager::Get();
  if(manager)
  {
    manager->RegisterAddOnDispatchTable(&table);
  }
}
```

Application developers usually consume add-ons through `Dali::Integration::AddOnManager::EnumerateAddOns()`, `Dali::Integration::AddOnManager::GetAddOnInfo()`, `Dali::Integration::AddOnManager::GetAddOn()`, `Dali::Integration::AddOnManager::GetGlobalProc()`, and `Dali::Integration::AddOnManager::GetInstanceProc()`. Add-on implementers provide a `Dali::AddOnDispatchTable` so the manager can discover and route those calls.
