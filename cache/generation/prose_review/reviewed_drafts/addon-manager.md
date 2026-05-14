---
title: Addon Manager
sidebar_label: Addon Manager
category: platform-integration
---

# Addon Manager

The Addon Manager provides a plugin architecture for extending DALi applications with dynamically loadable libraries. It enables applications to discover, load, and communicate with addons at runtime.

## Table of Contents

- [Discovering Available Addons](#discovering-available-addons)
- [Loading and Using Addons](#loading-and-using-addons)
- [Retrieving Addon Information](#retrieving-addon-information)
- [Binding Addon Functions](#binding-addon-functions)
- [Creating Custom Addons](#creating-custom-addons)
- [Addon Lifecycle Management](#addon-lifecycle-management)

## Discovering Available Addons

The `Dali::Integration::AddOnManager` singleton manages all addon operations. Use `EnumerateAddOns()` to retrieve a list of available addon names on the system.

```cpp
#include <dali/integration-api/addon-manager.h>

void ListAvailableAddons()
{
    Dali::Integration::AddOnManager* addonManager = Dali::Integration::AddOnManager::Get();
    if(!addonManager)
    {
        // AddonManager not available on this platform
        return;
    }

    std::vector<std::string> availableAddons = addonManager->EnumerateAddOns();
    for(const auto& name : availableAddons)
    {
        // Process each addon name
    }
}
```

Always check that `Dali::Integration::AddOnManager::Get()` returns a valid pointer before using the manager, as addon support may not be available on all platforms.

## Loading and Using Addons

### Loading Addons by Name

Use `LoadAddOns()` to load one or more addons by name. The method returns a vector of `Dali::AddOnLibrary` handles.

```cpp
#include <dali/integration-api/addon-manager.h>

void LoadMyAddons()
{
    Dali::Integration::AddOnManager* addonManager = Dali::Integration::AddOnManager::Get();
    if(!addonManager)
    {
        return;
    }

    std::vector<std::string> addonNames = {"MyImageLoader", "MyAudioPlugin"};
    std::vector<Dali::AddOnLibrary> handles = addonManager->LoadAddOns(addonNames);

    for(size_t i = 0; i < handles.size(); ++i)
    {
        if(handles[i])
        {
            // Addon loaded successfully
        }
    }
}
```

### Loading from a Specific Library

Use `LoadAddOn()` to load an addon from a specific library file path.

```cpp
#include <dali/integration-api/addon-manager.h>

Dali::AddOnLibrary LoadAddonFromLibrary(const std::string& addonName, const std::string& libraryPath)
{
    Dali::Integration::AddOnManager* addonManager = Dali::Integration::AddOnManager::Get();
    if(!addonManager)
    {
        return nullptr;
    }

    return addonManager->LoadAddOn(addonName, libraryPath);
}
```

### Getting an Already-Loaded Addon

Use `GetAddOn()` to retrieve a handle to an addon that may already be loaded.

```cpp
Dali::AddOnLibrary GetExistingAddon(const std::string& addonName)
{
    Dali::Integration::AddOnManager* addonManager = Dali::Integration::AddOnManager::Get();
    if(!addonManager)
    {
        return nullptr;
    }

    return addonManager->GetAddOn(addonName);
}
```

## Retrieving Addon Information

The `Dali::AddOnInfo` structure contains metadata about an addon, including its name, version, type, and build information.

```cpp
#include <dali/integration-api/addon-manager.h>

void PrintAddonInfo(const std::string& addonName)
{
    Dali::Integration::AddOnManager* addonManager = Dali::Integration::AddOnManager::Get();
    if(!addonManager)
    {
        return;
    }

    Dali::AddOnInfo info;
    if(addonManager->GetAddOnInfo(addonName, info))
    {
        // Access addon metadata
        std::string name = info.name;
        uint32_t version = info.version;
        Dali::AddOnType type = info.type;

        // Build information
        uint32_t coreVersion = info.buildInfo.libCoreVersion;
        uint32_t adaptorVersion = info.buildInfo.libAdaptorVersion;
        uint32_t toolkitVersion = info.buildInfo.libToolkitVersion;
    }
}
```

### Version Encoding

Use the `DALI_ADDON_VERSION` macro to encode version numbers into a 32-bit integer.

```cpp
uint32_t version = Dali::DALI_ADDON_VERSION(1, 2, 3);  // Major.Minor.Revision
```

## Binding Addon Functions

### Using AddOnBinder for Automatic Binding

The `Dali::AddOn::AddOnBinder` class simplifies binding addon functions to C++ member functions. Use the `ADDON_BIND_FUNCTION` macro to declare bound functions.

```cpp
#include <dali/devel-api/common/addon-binder.h>

struct MyImageLoader : public Dali::AddOn::AddOnBinder
{
    MyImageLoader()
    : Dali::AddOn::AddOnBinder("MyImageLoaderAddon")
    {
    }

    // Bind addon functions with their signatures
    ADDON_BIND_FUNCTION(LoadImage, bool(const std::string&, Dali::Devel::PixelBuffer&));
    ADDON_BIND_FUNCTION(GetImageSize, void(unsigned int&, unsigned int&));
};

void UseImageLoader()
{
    MyImageLoader loader;

    if(loader.IsValid())
    {
        Dali::Devel::PixelBuffer buffer;
        if(loader.LoadImage("image.png", buffer))
        {
            // Image loaded successfully
        }
    }
}
```

### Manual Function Retrieval

For more control, use `GetGlobalProc()` and `GetInstanceProc()` to retrieve function pointers directly.

```cpp
#include <dali/integration-api/addon-manager.h>

void UseManualBinding(Dali::AddOnLibrary handle)
{
    Dali::Integration::AddOnManager* addonManager = Dali::Integration::AddOnManager::Get();
    if(!addonManager)
    {
        return;
    }

    // Get typed global function
    auto processFunc = addonManager->GetGlobalProc<int(int, int)>(handle, "ProcessData");
    if(processFunc)
    {
        int result = processFunc(10, 20);
    }

    // Invoke global function directly
    int result = addonManager->InvokeGlobalProc<int, int, int>(handle, "AddNumbers", 5, 7);
}
```

## Creating Custom Addons

### Implementing AddOnBase

Derive from `Dali::AddOns::AddOnBase` to create a custom addon. Implement the required pure virtual methods and use the `REGISTER_ADDON_CLASS` macro to register the addon.

```cpp
#include <dali/devel-api/addons/addon-base.h>

// Global functions to expose
int CalculateSum(int a, int b)
{
    return a + b;
}

const char* GetVersion()
{
    return "1.0.0";
}

class MyCustomAddOn : public Dali::AddOns::AddOnBase
{
public:
    void GetAddOnInfo(Dali::AddOnInfo& info) override
    {
        info.type = Dali::AddOnType::GENERIC;
        info.name = "MyCustomAddOn";
        info.version = Dali::DALI_ADDON_VERSION(1, 0, 0);
        info.next = nullptr;
    }

    Dali::AddOns::DispatchTable* GetGlobalDispatchTable() override
    {
        static Dali::AddOns::DispatchTable dispatchTable{};
        if(dispatchTable.Empty())
        {
            dispatchTable["CalculateSum"] = CalculateSum;
            dispatchTable["GetVersion"] = GetVersion;
        }
        return &dispatchTable;
    }

    Dali::AddOns::DispatchTable* GetInstanceDispatchTable() override
    {
        return nullptr;  // No instance functions
    }

    void OnStart() override
    {
        // Called when the addon starts
    }

    void OnStop() override
    {
        // Called when the addon stops
    }
};

// Register the addon class
REGISTER_ADDON_CLASS(MyCustomAddOn);
```

### Using the Dispatch Table

The `Dali::AddOns::DispatchTable` maps function names to function pointers. Populate it in `GetGlobalDispatchTable()` for global functions or `GetInstanceDispatchTable()` for instance methods.

```cpp
Dali::AddOns::DispatchTable* GetGlobalDispatchTable() override
{
    static Dali::AddOns::DispatchTable dispatchTable{};
    if(dispatchTable.Empty())
    {
        // Register each function with its name
        dispatchTable["FunctionA"] = FunctionA;
        dispatchTable["FunctionB"] = FunctionB;
    }
    return &dispatchTable;
}
```

## Addon Lifecycle Management

The Addon Manager propagates application lifecycle events to all registered addons. The adaptor calls these methods automatically during application lifecycle transitions.

### Lifecycle Callbacks

Addons can respond to lifecycle events by overriding the virtual methods in `Dali::AddOns::AddOnBase`:

- `OnStart()` - Called when the application starts
- `OnResume()` - Called when the application resumes from background
- `OnPause()` - Called when the application goes to background
- `OnStop()` - Called when the application stops

```cpp
class LifecycleAwareAddOn : public Dali::AddOns::AddOnBase
{
public:
    void OnStart() override
    {
        // Initialize resources
    }

    void OnResume() override
    {
        // Resume operations
    }

    void OnPause() override
    {
        // Save state, release resources
    }

    void OnStop() override
    {
        // Clean up
    }

    // ... other required methods
};
```

### Manager Lifecycle Methods

The `Dali::Integration::AddOnManager` provides lifecycle methods that propagate events to all loaded addons:

```cpp
// Called by the adaptor during application lifecycle
addonManager->Start();   // Propagates OnStart to all addons
addonManager->Resume();  // Propagates OnResume to all addons
addonManager->Pause();   // Propagates OnPause to all addons
addonManager->Stop();    // Propagates OnStop to all addons
```

### Registering Dispatch Tables

Addons self-register by calling `RegisterAddOnDispatchTable()` with a pointer to their `Dali::AddOnDispatchTable`. The `REGISTER_ADDON_CLASS` macro handles this automatically.

```cpp
// Manual registration (normally handled by REGISTER_ADDON_CLASS macro)
Dali::AddOnDispatchTable table;
table.name = "MyAddOn";
table.GetAddOnInfo = GetAddOnInfoFunction;
table.GetGlobalProc = GetGlobalProcFunction;
table.GetInstanceProc = GetInstanceProcFunction;
table.OnStart = OnStartFunction;
table.OnStop = OnStopFunction;
table.OnResume = OnResumeFunction;
table.OnPause = OnPauseFunction;

Dali::Integration::AddOnManager::Get()->RegisterAddOnDispatchTable(&table);
