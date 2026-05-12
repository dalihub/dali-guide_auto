---
title: Addon Manager
sidebar_label: Addon Manager
category: uncategorized
---

# Addon Manager

The AddOn Manager provides a singleton-based system for discovering, loading, and managing extension modules (AddOns) in DALi applications. It handles the complete lifecycle of AddOns from enumeration through function binding.

## Table of Contents

- [Enumerating Available AddOns](#enumerating-available-addons)
- [Querying AddOn Information](#querying-addon-information)
- [Loading AddOns](#loading-addons)
- [Retrieving AddOn Functions](#retrieving-addon-functions)
- [AddOn Lifecycle Management](#addon-lifecycle-management)
- [Creating Custom AddOns](#creating-custom-addons)

## Enumerating Available AddOns

The `Dali::Integration::AddOnManager` singleton allows applications to discover available AddOns at runtime. Use `EnumerateAddOns()` to retrieve a list of all registered AddOn names.

```cpp
#include <dali/integration-api/addon-manager.h>

void ListAvailableAddOns()
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(manager)
  {
    std::vector<std::string> addons = manager->EnumerateAddOns();
    for(const auto& name : addons)
    {
      printf("Available AddOn: %s\n", name.c_str());
    }
  }
}
```

The `EnumerateAddOns()` method returns a `std::vector<std::string>` containing the names of all AddOns currently registered with the system.

## Querying AddOn Information

Each AddOn exposes metadata through the `Dali::AddOnInfo` structure. Use `GetAddOnInfo()` to retrieve detailed information about a specific AddOn.

```cpp
#include <dali/integration-api/addon-manager.h>

void PrintAddOnDetails(const std::string& addonName)
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(manager)
  {
    Dali::AddOnInfo info;
    if(manager->GetAddOnInfo(addonName, info))
    {
      printf("AddOn Name: %s\n", info.name.c_str());
      printf("Version: 0x%08X\n", info.version);
      printf("Type: %d\n", static_cast<int>(info.type));
      printf("Core Version: 0x%08X\n", info.buildInfo.libCoreVersion);
      printf("Adaptor Version: 0x%08X\n", info.buildInfo.libAdaptorVersion);
      printf("Toolkit Version: 0x%08X\n", info.buildInfo.libToolkitVersion);
    }
  }
}
```

The `Dali::AddOnInfo` structure contains:

| Field | Type | Description |
|-------|------|-------------|
| `name` | `std::string` | Name of the AddOn |
| `version` | `uint32_t` | AddOn version encoded using `DALI_ADDON_VERSION()` |
| `type` | `Dali::AddOnType` | Classification of the AddOn |
| `buildInfo.libCoreVersion` | `uint32_t` | DALi core library version |
| `buildInfo.libAdaptorVersion` | `uint32_t` | DALi adaptor library version |
| `buildInfo.libToolkitVersion` | `uint32_t` | DALi toolkit library version |
| `next` | `void*` | Pointer to additional data structures |

Use the `DALI_ADDON_VERSION(maj, min, rev)` macro to encode version numbers as 32-bit integers:

```cpp
uint32_t version = DALI_ADDON_VERSION(1, 2, 345);  // Encodes 1.2.345
```

## Loading AddOns

AddOns must be loaded before their functions can be used. The `AddOnManager` provides multiple methods for loading AddOns.

### Loading by Name

Use `GetAddOn()` to load an AddOn by its registered name:

```cpp
#include <dali/integration-api/addon-manager.h>

Dali::AddOnLibrary LoadAddOnByName(const std::string& name)
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(manager)
  {
    return manager->GetAddOn(name);
  }
  return nullptr;
}
```

### Loading Multiple AddOns

Use `LoadAddOns()` to load multiple AddOns in a single call:

```cpp
#include <dali/integration-api/addon-manager.h>

void LoadMultipleAddOns()
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(manager)
  {
    std::vector<std::string> names = {"ImageLoaderAddOn", "NetworkAddOn"};
    std::vector<Dali::AddOnLibrary> handles = manager->LoadAddOns(names);
    
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

### Loading from a Specific Library

Use `LoadAddOn()` to load an AddOn from a specific library file:

```cpp
#include <dali/integration-api/addon-manager.h>

Dali::AddOnLibrary LoadFromLibrary(const std::string& addonName, 
                                    const std::string& libraryName)
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(manager)
  {
    return manager->LoadAddOn(addonName, libraryName);
  }
  return nullptr;
}
```

## Retrieving AddOn Functions

Once an AddOn is loaded, retrieve function pointers using `GetGlobalProc()` for global functions or `GetInstanceProc()` for instance functions.

### Retrieving Global Functions

Global functions are standalone functions exported by the AddOn:

```cpp
#include <dali/integration-api/addon-manager.h>

// Define the function signature
using CalculateFunc = int(int, int);

void UseGlobalFunction(Dali::AddOnLibrary handle)
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(manager && handle)
  {
    // Get typed function
    std::function<CalculateFunc> calcFunc = 
        manager->GetGlobalProc<CalculateFunc>(handle, "Calculate");
    
    if(calcFunc)
    {
      int result = calcFunc(10, 20);
      printf("Result: %d\n", result);
    }
  }
}
```

### Retrieving Instance Functions

Instance functions operate on AddOn-specific context:

```cpp
#include <dali/integration-api/addon-manager.h>

using CreateContextFunc = void*();
using ProcessFunc = void(void*, int);

void UseInstanceFunctions(Dali::AddOnLibrary handle)
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(manager && handle)
  {
    auto createFunc = manager->GetInstanceProc<CreateContextFunc>(handle, "CreateContext");
    auto processFunc = manager->GetInstanceProc<ProcessFunc>(handle, "Process");
    
    if(createFunc && processFunc)
    {
      void* context = createFunc();
      processFunc(context, 42);
    }
  }
}
```

### Invoking Functions Directly

Use `InvokeGlobalProc()` to retrieve and invoke a function in a single call:

```cpp
#include <dali/integration-api/addon-manager.h>

void InvokeDirectly(Dali::AddOnLibrary handle)
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(manager && handle)
  {
    // Directly invoke a function that returns int and takes two int parameters
    int result = manager->InvokeGlobalProc<int(int, int)>(handle, "Add", 5, 7);
    printf("5 + 7 = %d\n", result);
  }
}
```

## AddOn Lifecycle Management

The `AddOnManager` propagates application lifecycle events to all registered AddOns. The Adaptor calls these methods automatically during application state transitions.

```cpp
// Lifecycle methods called by the Adaptor
void OnApplicationStart()
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(manager)
  {
    manager->Start();  // Notifies all AddOns
  }
}

void OnApplicationResume()
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(manager)
  {
    manager->Resume();  // Notifies all AddOns
  }
}

void OnApplicationPause()
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(manager)
  {
    manager->Pause();  // Notifies all AddOns
  }
}

void OnApplicationStop()
{
  auto* manager = Dali::Integration::AddOnManager::Get();
  if(manager)
  {
    manager->Stop();  // Notifies all AddOns
  }
}
```

## Creating Custom AddOns

To create a custom AddOn, derive from `Dali::AddOns::AddOnBase` and use the `REGISTER_ADDON_CLASS` macro for automatic registration.

### Implementing an AddOn

```cpp
#include <dali/devel-api/addons/addon-base.h>

class MyImageLoaderAddOn : public Dali::AddOns::AddOnBase
{
public:
  MyImageLoaderAddOn() = default;
  ~MyImageLoaderAddOn() override = default;

  void GetAddOnInfo(Dali::AddOnInfo& addonInfo) override
  {
    addonInfo.name = "MyImageLoader";
    addonInfo.type = Dali::AddOnType::IMAGE_LOADER;
    addonInfo.version = DALI_ADDON_VERSION(1, 0, 0);
    addonInfo.buildInfo.libCoreVersion = DALI_ADDON_VERSION(1, 0, 0);
    addonInfo.buildInfo.libAdaptorVersion = DALI_ADDON_VERSION(1, 0, 0);
    addonInfo.buildInfo.libToolkitVersion = DALI_ADDON_VERSION(1, 0, 0);
  }

  Dali::AddOns::DispatchTable* GetGlobalDispatchTable() override
  {
    return &mGlobalDispatchTable;
  }

  Dali::AddOns::DispatchTable* GetInstanceDispatchTable() override
  {
    return &mInstanceDispatchTable;
  }

  void OnStart() override
  {
    // Initialize resources when application starts
  }

  void OnStop() override
  {
    // Clean up resources when application stops
  }

private:
  Dali::AddOns::DispatchTable mGlobalDispatchTable;
  Dali::AddOns::DispatchTable mInstanceDispatchTable;
};

// Register the AddOn automatically
REGISTER_ADDON_CLASS(MyImageLoaderAddOn)
```

### Registering Functions in the Dispatch Table

Populate the dispatch table with function bindings:

```cpp
// Global function example
bool LoadImage(const char* path, void** pixels, int* width, int* height)
{
  // Implementation
  return true;
}

// Inside AddOn initialization
void SetupDispatchTable()
{
  mGlobalDispatchTable["LoadImage"] = LoadImage;
}
```

### Using the AddOnBinder Helper Class

The `Dali::AddOn::AddOnBinder` class simplifies binding AddOn functions in client code:

```cpp
#include <dali/devel-api/common/addon-binder.h>

struct ImageLoaderAddOn : public Dali::AddOn::AddOnBinder
{
  ImageLoaderAddOn()
  : Dali::AddOn::AddOnBinder("MyImageLoader")
  {
  }

  ~ImageLoaderAddOn() override = default;

  // Bind functions using the macro
  ADDON_BIND_FUNCTION(LoadImage, bool(const char*, void**, int*, int*));
  ADDON_BIND_FUNCTION(GetFormatExtension, const char*());
};

void UseAddOnBinder()
{
  ImageLoaderAddOn loader;
  
  if(loader.IsValid())
  {
    void* pixels = nullptr;
    int width = 0, height = 0;
    
    if(loader.LoadImage("image.png", &pixels, &width, &height))
    {
      printf("Loaded image: %dx%d\n", width, height);
    }
  }
}
```

The `ADDON_BIND_FUNCTION` macro creates a `std::function` member that automatically binds to the corresponding AddOn function by name.
