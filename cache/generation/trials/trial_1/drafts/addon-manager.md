---
title: Addon Manager
sidebar_label: Addon Manager
category: uncategorized
---

# Addon Manager

The Addon Manager provides a plugin system for DALi applications to discover, load, and interact with optional extension modules called AddOns.

## Table of Contents

- [Discovering Available AddOns](#discovering-available-addons)
- [Loading and Using AddOns](#loading-and-using-addons)
- [Retrieving AddOn Information](#retrieving-addon-information)
- [Calling AddOn Functions](#calling-addon-functions)
- [Lifecycle Management](#lifecycle-management)
- [Creating Custom AddOns](#creating-custom-addons)

## Discovering Available AddOns

The `Dali::Integration::AddOnManager` singleton allows applications to query which AddOns are available on the system. Use `EnumerateAddOns()` to retrieve a list of AddOn names.

```cpp
#include <dali/integration-api/addon-manager.h>

void ListAvailableAddOns()
{
  auto* addonManager = Dali::Integration::AddOnManager::Get();
  if(addonManager)
  {
    std::vector<std::string> availableAddOns = addonManager->EnumerateAddOns();
    for(const auto& name : availableAddOns)
    {
      printf("Available AddOn: %s\n", name.c_str());
    }
  }
}
```

The `EnumerateAddOns()` method returns a vector of strings containing the names of all discovered AddOns. The discovery mechanism is platform-dependent and typically scans predefined directories or environment-specified paths.

## Loading and Using AddOns

### Loading a Single AddOn

To load an AddOn by name, use `GetAddOn()` which returns a handle of type `Dali::AddOnLibrary`. This handle is an opaque pointer used for subsequent function lookups.

```cpp
#include <dali/integration-api/addon-manager.h>

Dali::AddOnLibrary LoadMyAddOn(const std::string& addonName)
{
  auto* addonManager = Dali::Integration::AddOnManager::Get();
  if(addonManager)
  {
    Dali::AddOnLibrary handle = addonManager->GetAddOn(addonName);
    if(handle)
    {
      return handle;
    }
  }
  return nullptr;
}
```

### Loading from a Specific Library

To load an AddOn from a specific library file, use `LoadAddOn()` with both the AddOn name and library name.

```cpp
#include <dali/integration-api/addon-manager.h>

Dali::AddOnLibrary LoadAddOnFromLibrary(const std::string& addonName, const std::string& libraryName)
{
  auto* addonManager = Dali::Integration::AddOnManager::Get();
  if(addonManager)
  {
    return addonManager->LoadAddOn(addonName, libraryName);
  }
  return nullptr;
}
```

### Loading Multiple AddOns

For loading several AddOns at once, use `LoadAddOns()` which accepts a vector of names and returns a vector of handles.

```cpp
#include <dali/integration-api/addon-manager.h>

void LoadMultipleAddOns()
{
  auto* addonManager = Dali::Integration::AddOnManager::Get();
  if(addonManager)
  {
    std::vector<std::string> names = {"ImageLoaderAddOn", "AudioAddOn"};
    std::vector<Dali::AddOnLibrary> handles = addonManager->LoadAddOns(names);
    
    for(size_t i = 0; i < handles.size(); ++i)
    {
      if(handles[i])
      {
        printf("Loaded: %s\n", names[i].c_str());
      }
    }
  }
}
```

## Retrieving AddOn Information

The `Dali::AddOnInfo` structure contains metadata about an AddOn, including its name, version, type, and build compatibility information.

```cpp
#include <dali/integration-api/addon-manager.h>

void PrintAddOnInfo(const std::string& addonName)
{
  auto* addonManager = Dali::Integration::AddOnManager::Get();
  if(addonManager)
  {
    Dali::AddOnInfo info;
    if(addonManager->GetAddOnInfo(addonName, info))
    {
      printf("AddOn Name: %s\n", info.name.c_str());
      printf("Version: %u\n", info.version);
      printf("Type: %d\n", static_cast<int>(info.type));
      printf("Core Version: %u\n", info.buildInfo.libCoreVersion);
    }
  }
}
```

The `Dali::AddOnInfo` structure contains the following fields:

| Field | Type | Description |
|-------|------|-------------|
| `name` | `std::string` | Name of the AddOn |
| `version` | `uint32_t` | Version encoded using `DALI_ADDON_VERSION()` |
| `type` | `Dali::AddOnType` | Classification of the AddOn |
| `next` | `void*` | Pointer to additional data structures |
| `buildInfo.libCoreVersion` | `uint32_t` | DALi core library version |
| `buildInfo.libAdaptorVersion` | `uint32_t` | DALi adaptor library version |
| `buildInfo.libToolkitVersion` | `uint32_t` | DALi toolkit library version |

### Version Encoding

Use the `DALI_ADDON_VERSION()` macro to encode or compare version numbers:

```cpp
#include <dali/integration-api/addon-manager.h>

void CheckVersionCompatibility(const Dali::AddOnInfo& info)
{
  uint32_t expectedVersion = Dali::DALI_ADDON_VERSION(1, 0, 0);
  if(info.version == expectedVersion)
  {
    printf("Version matches expected 1.0.0\n");
  }
}
```

## Calling AddOn Functions

AddOns expose functionality through global and instance functions. The AddOnManager provides methods to retrieve these function pointers.

### Retrieving Global Functions

Global functions are standalone functions that do not require an instance. Use `GetGlobalProc()` to retrieve them.

```cpp
#include <dali/integration-api/addon-manager.h>
#include <functional>

void UseGlobalFunction(Dali::AddOnLibrary addonHandle)
{
  auto* addonManager = Dali::Integration::AddOnManager::Get();
  if(addonManager && addonHandle)
  {
    // Get typed function pointer
    std::function<bool()> getStatus = addonManager->GetGlobalProc<bool()>(addonHandle, "GetLifecycleStatus");
    
    if(getStatus)
    {
      bool status = getStatus();
      printf("Status: %s\n", status ? "true" : "false");
    }
  }
}
```

### Retrieving Instance Functions

Instance functions operate on specific object instances created by the AddOn. Use `GetInstanceProc()` to retrieve them.

```cpp
#include <dali/integration-api/addon-manager.h>
#include <functional>

void UseInstanceFunction(Dali::AddOnLibrary addonHandle)
{
  auto* addonManager = Dali::Integration::AddOnManager::Get();
  if(addonManager && addonHandle)
  {
    // Get factory function
    auto createInstance = addonManager->GetGlobalProc<void*()>(addonHandle, "CreateInstance");
    
    // Get instance method
    auto instanceCall = addonManager->GetInstanceProc<uint32_t(void*)>(addonHandle, "InstanceCall");
    
    if(createInstance && instanceCall)
    {
      void* instance = createInstance();
      uint32_t result = instanceCall(instance);
      printf("Result: %u\n", result);
    }
  }
}
```

### Using InvokeGlobalProc

For direct invocation without storing the function, use the template method `InvokeGlobalProc()`:

```cpp
#include <dali/integration-api/addon-manager.h>

void InvokeDirectly(Dali::AddOnLibrary addonHandle)
{
  auto* addonManager = Dali::Integration::AddOnManager::Get();
  if(addonManager && addonHandle)
  {
    // Directly invoke and get result
    bool status = addonManager->InvokeGlobalProc<bool>(addonHandle, "GetLifecycleStatus");
  }
}
```

### Using AddOnBinder Helper Class

The `Dali::AddOn::AddOnBinder` class simplifies binding AddOn functions as class members using the `ADDON_BIND_FUNCTION` macro.

```cpp
#include <dali/devel-api/common/addon-binder.h>

struct MyAddOnInterface : public Dali::AddOn::AddOnBinder
{
  MyAddOnInterface()
  : AddOnBinder("MyAddOn", 0u)  // Name and minimum version (0 = any)
  {
  }

  // Bind functions as member variables
  ADDON_BIND_FUNCTION(Initialize, bool());
  ADDON_BIND_FUNCTION(Process, void(int));
  ADDON_BIND_FUNCTION(GetResult, int());
};

void UseBinderInterface()
{
  MyAddOnInterface addon;
  
  if(addon.IsValid())
  {
    if(addon.Initialize)
    {
      addon.Initialize();
      addon.Process(42);
      int result = addon.GetResult();
    }
  }
}
```

## Lifecycle Management

The AddOnManager propagates application lifecycle events to loaded AddOns. Call these methods from your application's lifecycle handlers.

```cpp
#include <dali/integration-api/addon-manager.h>

class MyApp
{
public:
  void OnStart()
  {
    auto* addonManager = Dali::Integration::AddOnManager::Get();
    if(addonManager)
    {
      addonManager->Start();
    }
  }

  void OnResume()
  {
    auto* addonManager = Dali::Integration::AddOnManager::Get();
    if(addonManager)
    {
      addonManager->Resume();
    }
  }

  void OnPause()
  {
    auto* addonManager = Dali::Integration::AddOnManager::Get();
    if(addonManager)
    {
      addonManager->Pause();
    }
  }

  void OnStop()
  {
    auto* addonManager = Dali::Integration::AddOnManager::Get();
    if(addonManager)
    {
      addonManager->Stop();
    }
  }
};
```

These lifecycle calls trigger the corresponding callbacks in each AddOn's dispatch table, allowing AddOns to manage resources appropriately.

## Creating Custom AddOns

To create a custom AddOn, derive from `Dali::AddOns::AddOnBase` and use the registration macro.

### Implementing AddOnBase

```cpp
#include <dali/devel-api/addons/addon-base.h>

class MyCustomAddOn : public Dali::AddOns::AddOnBase
{
public:
  MyCustomAddOn() = default;
  ~MyCustomAddOn() override = default;

  void GetAddOnInfo(Dali::AddOnInfo& addonInfo) override
  {
    addonInfo.name = "MyCustomAddOn";
    addonInfo.version = Dali::DALI_ADDON_VERSION(1, 0, 0);
    addonInfo.type = Dali::AddOnType::GENERIC;
    addonInfo.buildInfo.libCoreVersion = DALI_CORE_VERSION;
  }

  Dali::AddOns::DispatchTable* GetGlobalDispatchTable() override
  {
    return &mGlobalTable;
  }

  Dali::AddOns::DispatchTable* GetInstanceDispatchTable() override
  {
    return &mInstanceTable;
  }

  void OnStart() override
  {
    // Initialize resources when application starts
  }

  void OnPause() override
  {
    // Release resources when application pauses
  }

  void OnResume() override
  {
    // Restore resources when application resumes
  }

  void OnStop() override
  {
    // Clean up when application stops
  }

private:
  Dali::AddOns::DispatchTable mGlobalTable;
  Dali::AddOns::DispatchTable mInstanceTable;
};

// Register the AddOn
REGISTER_ADDON_CLASS(MyCustomAddOn)
```

### Populating the Dispatch Table

Add functions to the dispatch table to expose them to applications:

```cpp
// Global function implementation
bool MyGlobalFunction()
{
  return true;
}

// Instance function implementation
void MyInstanceFunction(void* instance, int value)
{
  // Process with instance data
}

// In constructor or initialization:
mGlobalTable["MyGlobalFunction"] = MyGlobalFunction;
mInstanceTable["MyInstanceFunction"] = MyInstanceFunction;
```

### The AddOnDispatchTable Structure

For lower-level control, populate a `Dali::AddOnDispatchTable` directly:

```cpp
#include <dali/integration-api/addon-manager.h>

void GetAddOnInfo(Dali::AddOnInfo& info)
{
  info.name = "LowLevelAddOn";
  info.version = Dali::DALI_ADDON_VERSION(1, 0, 0);
  info.type = Dali::AddOnType::GENERIC;
}

void* GetGlobalProc(const char* funcName)
{
  if(strcmp(funcName, "MyFunction") == 0)
  {
    return reinterpret_cast<void*>(MyFunction);
  }
  return nullptr;
}

void OnStart() { /* ... */ }
void OnStop() { /* ... */ }
void OnPause() { /* ... */ }
void OnResume() { /* ... */ }

// Create dispatch table
Dali::AddOnDispatchTable dispatchTable;
dispatchTable.name = "LowLevelAddOn";
dispatchTable.GetAddOnInfo = GetAddOnInfo;
dispatchTable.GetGlobalProc = GetGlobalProc;
dispatchTable.OnStart = OnStart;
dispatchTable.OnStop = OnStop;
dispatchTable.OnPause = OnPause;
dispatchTable.OnResume = OnResume;

// Register with manager
Dali::Integration::AddOnManager::Get()->RegisterAddOnDispatchTable(&dispatchTable);
