-----BEGIN_REVISED_MARKDOWN-----
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

Use the `DALI_ADDON_VERSION()` constexpr function to encode or compare version numbers:

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
  : Dali::AddOn::AddOnBinder("MyAddOn", 0u) // Name and minimum version (0 = any)
  {
  }

  ~MyAddOnInterface() override = default;

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

The lifecycle methods (`OnStart()`, `OnResume()`, `OnPause()`, `OnStop()`) are optional and have empty default implementations.

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
    addonInfo.next = nullptr;
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

Add functions to the dispatch table using the subscript operator to expose them to applications:

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

// In GetGlobalDispatchTable() or initialization:
Dali::AddOns::DispatchTable* GetGlobalDispatchTable() override
{
  static Dali::AddOns::DispatchTable dispatchTable{};
  if(dispatchTable.Empty())
  {
    dispatchTable["MyGlobalFunction"] = MyGlobalFunction;
  }
  return &dispatchTable;
}
```

### The AddOnDispatchTable Structure

For lower-level control, populate a `Dali::AddOnDispatchTable` directly and register it with the AddOnManager. This structure contains function pointer fields for lifecycle callbacks and procedure lookup:

```cpp
#include <dali/integration-api/addon-manager.h>

void GetAddOnInfoCallback(Dali::AddOnInfo& info)
{
  info.name = "LowLevelAddOn";
  info.version = Dali::DALI_ADDON_VERSION(1, 0, 0);
  info.type = Dali::AddOnType::GENERIC;
  info.next = nullptr;
}

void* GetGlobalProcCallback(const char* funcName)
{
  if(strcmp(funcName, "MyFunction") == 0)
  {
    return reinterpret_cast<void*>(MyFunction);
  }
  return nullptr;
}

void OnStartCallback() { /* ... */ }
void OnStopCallback() { /* ... */ }
void OnPauseCallback() { /* ... */ }
void OnResumeCallback() { /* ... */ }

// Create dispatch table
Dali::AddOnDispatchTable dispatchTable;
dispatchTable.name = "LowLevelAddOn";
dispatchTable.GetAddOnInfo = GetAddOnInfoCallback;
dispatchTable.GetGlobalProc = GetGlobalProcCallback;
dispatchTable.OnStart = OnStartCallback;
dispatchTable.OnStop = OnStopCallback;
dispatchTable.OnPause = OnPauseCallback;
dispatchTable.OnResume = OnResumeCallback;

// Register with manager
Dali::Integration::AddOnManager::Get()->RegisterAddOnDispatchTable(&dispatchTable);
```

Note: The `RegisterAddOnDispatchTable()` method is intended for AddOn self-registration and is typically called automatically when using the `REGISTER_ADDON_CLASS()` macro.
-----END_REVISED_MARKDOWN-----

-----BEGIN_PROSE_REVIEW_REPORT-----
# Addon Manager Prose Review

## Summary

Reviewed the draft against `repos/dali-core/dali/integration-api/addon-manager.h`, `repos/dali-core/dali/devel-api/addons/addon-base.h`, `repos/dali-core/dali/devel-api/common/addon-binder.h`, and `repos/dali-core/dali/devel-api/addons/addon-dispatch-table.h`.

## Changes Made

### 1. DALI_ADDON_VERSION Description (Section: Version Encoding)
- **Original**: "Use the `DALI_ADDON_VERSION()` macro to encode or compare version numbers"
- **Revised**: "Use the `DALI_ADDON_VERSION()` constexpr function to encode or compare version numbers"
- **Source Evidence**: `addon-manager.h` line 46: `constexpr uint32_t DALI_ADDON_VERSION(uint32_t maj, uint32_t min, uint32_t rev)`
- **Reason**: `DALI_ADDON_VERSION` is a `constexpr` function, not a macro.

### 2. AddOnBase Lifecycle Methods Description (Section: Implementing AddOnBase)
- **Original**: No mention that lifecycle methods are optional
- **Revised**: Added sentence: "The lifecycle methods (`OnStart()`, `OnResume()`, `OnPause()`, `OnStop()`) are optional and have empty default implementations."
- **Source Evidence**: `addon-base.h` lines 62-91 show virtual methods with empty default implementations (`{ }`)
- **Reason**: Clarify that overriding lifecycle methods is optional.

### 3. DispatchTable Population Syntax (Section: Populating the Dispatch Table)
- **Original**: Showed incorrect syntax `mGlobalTable["MyGlobalFunction"] = MyGlobalFunction;` without context
- **Revised**: Rewrote to show correct pattern using static local variable and `Empty()` check, matching the test sample pattern
- **Source Evidence**: `test-sample-addon.cpp` lines 35-42:
  ```cpp
  static Dali::AddOns::DispatchTable dispatchTable{};
  if(dispatchTable.Empty())
  {
    dispatchTable["DoSum"] = DoSum;
    dispatchTable["StringLen"] = StringLen;
  }
  ```
- **Reason**: Original code was incomplete and misleading about how to populate dispatch tables.

### 4. AddOnDispatchTable Callback Naming (Section: The AddOnDispatchTable Structure)
- **Original**: Used function names like `GetAddOnInfo`, `OnStart` directly as both function names and field names
- **Revised**: Renamed callback functions to `GetAddOnInfoCallback`, `OnStartCallback`, etc. to avoid confusion with struct field names
- **Source Evidence**: `addon-manager.h` lines 72-80 show `AddOnDispatchTable` fields are function pointers
- **Reason**: Original code was confusing by using the same name for the callback function and the struct field.

### 5. AddOnBinder Destructor (Section: Using AddOnBinder Helper Class)
- **Original**: Missing destructor declaration
- **Revised**: Added `~MyAddOnInterface() override = default;`
- **Source Evidence**: `addon-binder.h` line 91: `virtual ~AddOnBinder() = default;` and UTC test pattern in `utc-Dali-AddOn.cpp` line 25
- **Reason**: Match the pattern shown in the header's example and UTC tests.

### 6. AddOnInfo next field initialization (Section: Implementing AddOnBase)
- **Original**: Missing `addonInfo.next = nullptr;`
- **Revised**: Added initialization of `next` field to `nullptr`
- **Source Evidence**: `test-sample-addon.cpp` line 32: `info.next = nullptr;`
- **Reason**: Complete example showing all required field initializations.

### 7. Clarification for RegisterAddOnDispatchTable (Section: The AddOnDispatchTable Structure)
- **Original**: No context about when to use this method
- **Revised**: Added note: "Note: The `RegisterAddOnDispatchTable()` method is intended for AddOn self-registration and is typically called automatically when using the `REGISTER_ADDON_CLASS()` macro."
- **Source Evidence**: `addon-base.h` lines 147-150 show automatic registration via `AddOnConstructorInternal()` when using `REGISTER_ADDON_CLASS`
- **Reason**: Clarify that manual registration is only needed for low-level scenarios.

## Verified Accurate Content

- `AddOnManager::Get()` returns singleton pointer
- `EnumerateAddOns()` returns `std::vector<std::string>`
- `GetAddOn()` is inline and calls `LoadAddOns({addonName})[0]`
- `AddOnLibrary` is `typedef void* AddOnLibrary`
- `AddOnBinder` is in namespace `Dali::AddOn`
- `AddOnBase` is in namespace `Dali::AddOns`
- `DispatchTable` (for AddOnBase) is in namespace `Dali::AddOns`
- `AddOnDispatchTable` (low-level) is in namespace `Dali`
- `ADDON_BIND_FUNCTION` macro syntax is correct
- `REGISTER_ADDON_CLASS` macro syntax is correct

## Remaining Concerns

None. All prose now accurately reflects the public API headers and implementation patterns shown in UTC tests.
-----END_PROSE_REVIEW_REPORT-----