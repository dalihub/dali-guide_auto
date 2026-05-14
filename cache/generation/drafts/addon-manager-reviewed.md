-----BEGIN_REVISED_MARKDOWN-----
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
constexpr uint32_t myVersion = DALI_ADDON_VERSION(1, 2, 300); // major, minor, revision
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

For more control, use `GetGlobalProc()` and `GetInstanceProc()` directly through the AddOnManager. Note that these methods return `void*` function pointers:

```cpp
#include <dali/integration-api/addon-manager.h>

void UseManualBinding(Dali::AddOnLibrary handle)
{
  Dali::Integration::AddOnManager* addOnManager = Dali::Integration::AddOnManager::Get();
  if(addOnManager && handle)
  {
    // Get raw function pointer
    void* proc = addOnManager->GetGlobalProc(handle, "ProcessInt");
    if(proc)
    {
      // Cast to appropriate function type
      using ProcessFunc = void(*)(int);
      ProcessFunc processFunc = reinterpret_cast<ProcessFunc>(proc);
      processFunc(42);
    }
  }
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

This macro generates the necessary constructor code and registers the AddOn with `Dali::Integration::AddOnManager::RegisterAddOnDispatchTable()`. The macro requires a GCC/Clang compiler and is currently only supported on Linux-based environments.

### Populating the Dispatch Table

The `Dali::AddOns::DispatchTable` class maps function names to function pointers. Populate it using the subscript operator:

```cpp
// In your AddOn initialization
mGlobalDispatchTable["ProcessData"] = ProcessData;
mGlobalDispatchTable["GetVersion"] = GetVersion;
```

The `REGISTER_ADDON_CLASS()` macro automatically creates a `Dali::AddOnDispatchTable` and registers it with the AddOnManager, binding the lifecycle callbacks and function lookup methods.

### AddOnDispatchTable Structure

The `Dali::AddOnDispatchTable` structure (defined in `<dali/integration-api/addon-manager.h>`) contains function pointers that the AddOnManager uses to interact with your AddOn:

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
-----END_REVISED_MARKDOWN-----

-----BEGIN_PROSE_REVIEW_REPORT-----
# Addon Manager Prose Review

## Summary

Reviewed the Addon Manager guide draft against public headers in `repos/dali-core/dali/integration-api/addon-manager.h`, `repos/dali-core/dali/devel-api/common/addon-binder.h`, `repos/dali-core/dali/devel-api/addons/addon-base.h`, and `repos/dali-core/dali/devel-api/addons/addon-dispatch-table.h`.

## Changes Made

### 1. Removed `InvokeGlobalProc()` Section
**Location**: "Function Binding with AddOnBinder" section
**Issue**: The `InvokeGlobalProc<R, Args...>` template method is marked `DALI_INTERNAL` in the header, indicating it is an internal API not intended for application use.
**Change**: Removed the "Invoking Functions Directly" subsection that demonstrated `InvokeGlobalProc()`.
**Source Evidence**: `addon-manager.h` line 181: `template<class R, class... Args> DALI_INTERNAL R InvokeGlobalProc(...)`

### 2. Clarified Template `GetGlobalProc`/`GetInstanceProc` Methods
**Location**: "Manual Function Retrieval" subsection
**Issue**: The template versions `GetGlobalProc<T>` and `GetInstanceProc<T>` are marked `DALI_INTERNAL`. The draft showed them as regular public API.
**Change**: Rewrote the example to use the non-template `void*` returning versions, with explicit casting. Added note that these return `void*` function pointers.
**Source Evidence**: `addon-manager.h` lines 147 and 161 show `DALI_INTERNAL` marker on template versions.

### 3. Clarified Dispatch Table Types
**Location**: "Creating Custom AddOns" section
**Issue**: The draft conflated two different dispatch table types:
- `Dali::AddOnDispatchTable` (in `addon-manager.h`) - used for registration with AddOnManager
- `Dali::AddOns::DispatchTable` (in `addon-dispatch-table.h`) - used by AddOnBase for function lookup
**Change**: 
- Made clear that `AddOnBase` methods return `Dali::AddOns::DispatchTable*`
- Added new subsection "Populating the Dispatch Table" showing how to populate `Dali::AddOns::DispatchTable` using subscript operator
- Clarified that `REGISTER_ADDON_CLASS()` handles creation and registration of `Dali::AddOnDispatchTable` automatically
**Source Evidence**: 
- `addon-base.h` line 50: `virtual DispatchTable* GetGlobalDispatchTable() = 0;`
- `addon-dispatch-table.h` defines `Dali::AddOns::DispatchTable` with `operator[]` and `Find()` methods
- `addon-base.h` `AddOnConstructorInternal()` function shows automatic dispatch table population

### 4. Removed Manual Dispatch Table Population Example
**Location**: End of "Creating Custom AddOns" section
**Issue**: The draft showed manual population of `Dali::AddOnDispatchTable` which is misleading - this is handled automatically by `REGISTER_ADDON_CLASS` macro via `AddOnConstructorInternal()`.
**Change**: Removed the misleading manual population code. Added clarification that `REGISTER_ADDON_CLASS()` handles this automatically.
**Source Evidence**: `addon-base.h` lines 147-161 show `AddOnConstructorInternal()` creates and populates the `Dali::AddOnDispatchTable` automatically.

### 5. Added Platform Limitation Note
**Location**: "Registering the AddOn" subsection
**Issue**: The `REGISTER_ADDON_CLASS` macro has platform limitations not mentioned in the draft.
**Change**: Added note that the macro requires GCC/Clang compiler and is currently only supported on Linux-based environments.
**Source Evidence**: `addon-base.h` line 180 comment: "Note: The macro requires GCC/Clang compiler and currently only Linux-based environment is supported."

### 6. Fixed AddOnBinder Constructor Example
**Location**: "Loading from a Specific Library" subsection
**Issue**: The example showed a constructor taking only `libraryPath` but the actual constructor requires `addonName` as the first parameter.
**Change**: Fixed the example to pass both `addonName` and `libraryName` to the base constructor.
**Source Evidence**: `addon-binder.h` line 77: `explicit AddOnBinder(const char* addonName, const char* libraryName, uint32_t version = 0u)`

## Preserved Content

- All code blocks preserved with minimal corrections
- Section structure unchanged (no section restructuring)
- Table of Contents preserved
- AddOnInfo structure table preserved
- AddOnDispatchTable structure table preserved (moved to end of Creating Custom AddOns section for context)
- Lifecycle management table and examples preserved

## Remaining Concerns

1. **Internal API Usage**: The `AddOnBinder::ConvertFunction<T>()` template method is used internally by `ADDON_BIND_FUNCTION` macro but is not marked `DALI_INTERNAL`. Application developers should use the macro rather than calling this method directly.

2. **AddOnType Enum**: The `Dali::AddOnType` enum has `GENERIC` and `IMAGE_LOADER` values. The draft mentions this but doesn't explain when to use each type. This is acceptable as the enum is primarily for internal classification.

3. **Version Parameter in AddOnBinder**: The `AddOnBinder` constructors accept a `version` parameter (defaults to 0 for any version), but the draft examples don't demonstrate version checking. This is acceptable for a basic guide.

## Verification

All API names, namespaces, method signatures, and structure fields verified against:
- `repos/dali-core/dali/integration-api/addon-manager.h`
- `repos/dali-core/dali/devel-api/common/addon-binder.h`
- `repos/dali-core/dali/devel-api/addons/addon-base.h`
- `repos/dali-core/dali/devel-api/addons/addon-dispatch-table.h`
- `repos/dali-core/automated-tests/src/dali/utc-Dali-AddOn.cpp`
-----END_PROSE_REVIEW_REPORT-----
<task_progress>
- [x] Read addon-manager.h public header
- [x] Search for addon-binder.h and addon-base.h headers
- [x] Read addon-binder.h header
- [x] Read addon-base.h header
- [x] Read addon-dispatch-table.h header
- [x] Read UTC tests for usage patterns
- [x] Review draft against source evidence
- [x] Make localized edits for accuracy
- [x] Output revised markdown and review report
</task_progress>
</write_to_file>