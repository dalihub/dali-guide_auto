---
title: Addon Manager
sidebar_label: Addon Manager
category: platform-integration
---

# Addon Manager

The Addon Manager provides a plugin architecture for extending DALi applications with dynamically loadable modules called AddOns. It handles discovery, loading, lifecycle management, and function binding for external libraries.

## Table of Contents

- [Discovering Available AddOns](#discovering-available-addons)
- [Loading and Using AddOns](#loading-and-using-addons)
- [Retrieving AddOn Information](#retrieving-addon-information)
- [Function Binding with AddOnBinder](#function-binding-with-addonbinder)
- [AddOn Lifecycle Management](#addon-lifecycle-management)
- [Creating Custom AddOns](#creating-custom-addons)

## Discovering Available AddOns

The `Dali::Integration::AddOnManager` singleton provides access to all available AddOns on the system. Use `EnumerateAddOns()` to retrieve a list of AddOn names.

```cpp
#include <dali/integration-api/addon-manager.h>

void ListAvailableAddOns()
{
    Dali::Integration::AddOnManager* addOnManager = Dali::Integration::AddOnManager::Get();
    if(addOnManager)
    {
        std::vector<std::string> addOnNames = addOnManager->EnumerateAddOns();
        for(const auto& name : addOnNames)
        {
            std::cout << "Available AddOn: " << name << std::endl;
        }
    }
}
```

Always check that `Dali::Integration::AddOnManager::Get()` returns a valid pointer before using the manager. The AddOnManager is created by the Adaptor and may not be available on all platforms.

## Loading and Using AddOns

### Loading a Single AddOn

Use `GetAddOn()` to load an AddOn by name. The method returns a `Dali::AddOnLibrary` handle that identifies the loaded library.

```cpp
#include <dali/integration-api/addon-manager.h>

Dali::AddOnLibrary LoadMyAddOn(const std::string& addOnName)
{
    Dali::Integration::AddOnManager* addOnManager = Dali::Integration::AddOnManager::Get();
    if(addOnManager)
    {
        Dali::AddOnLibrary handle = addOnManager->GetAddOn(addOnName);
        if(handle)
        {
            std::cout << "AddOn loaded successfully: " << addOnName << std::endl;
            return handle;
        }
    }
    return nullptr;
}
```

### Loading from a Specific Library

Use `LoadAddOn()` when you need to specify the exact library file name:

```cpp
#include <dali/integration-api/addon-manager.h>

Dali::AddOnLibrary LoadAddOnFromLibrary(const std::string& addOnName, const std::string& libraryName)
{
    Dali::Integration::AddOnManager* addOnManager = Dali::Integration::AddOnManager::Get();
    if(addOnManager)
    {
        return addOnManager->LoadAddOn(addOnName, libraryName);
    }
    return nullptr;
}
```

### Loading Multiple AddOns

To load several AddOns at once, use `LoadAddOns()`:

```cpp
#include <dali/integration-api/addon-manager.h>

void LoadMultipleAddOns()
{
    Dali::Integration::AddOnManager* addOnManager = Dali::Integration::AddOnManager::Get();
    if(addOnManager)
    {
        std::vector<std::string> addOnNames = {"ImageLoaderExt", "CustomRenderer"};
        std::vector<Dali::AddOnLibrary> handles = addOnManager->LoadAddOns(addOnNames);
        
        for(size_t i = 0; i < handles.size(); ++i)
        {
            if(handles[i])
            {
                std::cout << "Loaded: " << addOnNames[i] << std::endl;
            }
        }
    }
}
```

## Retrieving AddOn Information

The `Dali::AddOnInfo` structure contains metadata about an AddOn, including its name, version, type, and build compatibility information.

### AddOnInfo Structure

| Field | Type | Description |
|-------|------|-------------|
| `name` | `std::string` | Name of the AddOn |
| `version` | `uint32_t` | Version number encoded using `DALI_ADDON_VERSION()` |
| `type` | `Dali::AddOnType` | Classification of the AddOn |
| `next` | `void*` | Pointer to additional data structures |
| `buildInfo.libCoreVersion` | `uint32_t` | DALi core library version |
| `buildInfo.libAdaptorVersion` | `uint32_t` | DALi adaptor library version |
| `buildInfo.libToolkitVersion` | `uint32_t` | DALi toolkit library version |

### Getting AddOn Information

```cpp
#include <dali/integration-api/addon-manager.h>

void PrintAddOnInfo(const std::string& addOnName)
{
    Dali::Integration::AddOnManager* addOnManager = Dali::Integration::AddOnManager::Get();
    if(addOnManager)
    {
        Dali::AddOnInfo info;
        if(addOnManager->GetAddOnInfo(addOnName, info))
        {
            std::cout << "AddOn Name: " << info.name << std::endl;
            std::cout << "Version: " << info.version << std::endl;
            std::cout << "Core Version: " << info.buildInfo.libCoreVersion << std::endl;
        }
    }
}
```

### Version Encoding

Use the `DALI_ADDON_VERSION()` macro to encode version numbers for comparison:

```cpp
constexpr uint32_t myVersion = DALI_ADDON_VERSION(1, 2, 300);  // major, minor, revision
```

## Function Binding with AddOnBinder

The `Dali::AddOn::AddOnBinder` class simplifies binding AddOn functions to your application. It handles loading, function lookup, and provides a clean interface.

### Basic AddOnBinder Usage

Create a structure that inherits from `Dali::AddOn::AddOnBinder` and use the `ADDON_BIND_FUNCTION()` macro to declare bound functions:

```cpp
#include <dali/devel-api/common/addon-binder.h>

struct ImageLoaderAddOn : public Dali::AddOn::AddOnBinder
{
    ImageLoaderAddOn()
    : Dali::AddOn::AddOnBinder("ImageLoaderExt")
    {
    }

    ~ImageLoaderAddOn() override = default;

    // Bind functions from the AddOn
    ADDON_BIND_FUNCTION(LoadBitmap, bool(const std::string&, Dali::PixelBuffer&));
    ADDON_BIND_FUNCTION(GetFormatExtension, const char*());
};

void UseImageLoaderAddOn()
{
    ImageLoaderAddOn addon;
    
    if(addon.IsValid())
    {
        // Call bound functions directly
        const char* extension = addon.GetFormatExtension();
        std::cout << "Supported extension: " << extension << std::endl;
    }
}
```

### Loading from a Specific Library

```cpp
struct CustomAddOn : public Dali::AddOn::AddOnBinder
{
    CustomAddOn(const char* libraryPath)
    : Dali::AddOn::AddOnBinder("CustomAddOn", libraryPath)
    {
    }

    ADDON_BIND_FUNCTION(ProcessData, void(const uint8_t*, size_t));
};
```

### Accessing AddOn Information

```cpp
void CheckAddOnDetails(Dali::AddOn::AddOnBinder& addon)
{
    if(addon.IsValid())
    {
        const Dali::AddOnInfo& info = addon.GetAddOnInfo();
        std::cout << "Using AddOn: " << info.name << std::endl;
    }
}
```

### Manual Function Retrieval

For more control, use `GetGlobalProc()` and `GetInstanceProc()` directly:

```cpp
#include <dali/integration-api/addon-manager.h>

void UseManualBinding(Dali::AddOnLibrary handle)
{
    Dali::Integration::AddOnManager* addOnManager = Dali::Integration::AddOnManager::Get();
    if(addOnManager && handle)
    {
        // Get typed function pointer
        auto processFunc = addOnManager->GetGlobalProc<void(int)>(handle, "ProcessInt");
        if(processFunc)
        {
            processFunc(42);
        }
    }
}
```

### Invoking Functions Directly

Use `InvokeGlobalProc()` for one-shot function calls:

```cpp
#include <dali/integration-api/addon-manager.h>

int InvokeAddOnFunction(Dali::AddOnLibrary handle, int a, int b)
{
    Dali::Integration::AddOnManager* addOnManager = Dali::Integration::AddOnManager::Get();
    if(addOnManager && handle)
    {
        return addOnManager->InvokeGlobalProc<int, int, int>(handle, "AddNumbers", a, b);
    }
    return 0;
}
```

## AddOn Lifecycle Management

The AddOnManager propagates application lifecycle events to loaded AddOns. The Adaptor calls these methods automatically.

| Method | When Called |
|--------|-------------|
| `Start()` | Application starts |
| `Resume()` | Application resumes from background |
| `Pause()` | Application goes to background |
| `Stop()` | Application terminates |

```cpp
#include <dali/integration-api/addon-manager.h>

void OnApplicationPause()
{
    Dali::Integration::AddOnManager* addOnManager = Dali::Integration::AddOnManager::Get();
    if(addOnManager)
    {
        addOnManager->Pause();
    }
}

void OnApplicationResume()
{
    Dali::Integration::AddOnManager* addOnManager = Dali::Integration::AddOnManager::Get();
    if(addOnManager)
    {
        addOnManager->Resume();
    }
}
```

## Creating Custom AddOns

To create an AddOn that can be loaded by the AddOnManager, implement `Dali::AddOns::AddOnBase` and use the registration macro.

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
        addonInfo.version = DALI_ADDON_VERSION(1, 0, 0);
        addonInfo.type = Dali::AddOnType::GENERIC;
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
```

### Registering the AddOn

Use the `REGISTER_ADDON_CLASS()` macro to auto-register your AddOn:

```cpp
REGISTER_ADDON_CLASS(MyCustomAddOn)
```

This macro generates the necessary constructor code and registration with `Dali::Integration::AddOnManager::RegisterAddOnDispatchTable()`.

### Dispatch Table Structure

The `Dali::AddOnDispatchTable` structure contains function pointers that the AddOnManager uses to interact with your AddOn:

| Field | Type | Description |
|-------|------|-------------|
| `name` | `std::string` | AddOn name |
| `GetAddOnInfo` | `void(*)(Dali::AddOnInfo&)` | Returns AddOn metadata |
| `GetGlobalProc` | `void*(*)(const char*)` | Returns global function pointer |
| `GetInstanceProc` | `void*(*)(const char*)` | Returns instance function pointer |
| `OnStart` | `void(*)()` | Called on application start |
| `OnResume` | `void(*)()` | Called on application resume |
| `OnPause` | `void(*)()` | Called on application pause |
| `OnStop` | `void(*)()` | Called on application stop |

### Populating the Dispatch Table

```cpp
Dali::AddOnDispatchTable table;
table.name = "MyAddOn";
table.GetAddOnInfo = GetAddOnInfo<void>;
table.GetGlobalProc = GetGlobalProc<void>;
table.GetInstanceProc = GetInstanceProc<void>;
table.OnStart = OnStart<void>;
table.OnStop = OnStop<void>;
table.OnResume = OnResume<void>;
table.OnPause = OnPause<void>;

Dali::Integration::AddOnManager::Get()->RegisterAddOnDispatchTable(&table);
