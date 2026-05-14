---
title: Addon Manager
sidebar_label: Addon Manager
category: platform-integration
---

# Addon Manager

Addon Manager lets DALi application integration code discover add-ons, load them, inspect their metadata, call exported procedures, and forward application lifecycle events.

## Table of Contents

- [Accessing the Addon Manager](#accessing-the-addon-manager)
- [Discovering and Inspecting Add-ons](#discovering-and-inspecting-add-ons)
- [Loading Add-ons and Calling Procedures](#loading-add-ons-and-calling-procedures)
- [Coordinating Add-on Lifecycle](#coordinating-add-on-lifecycle)
- [Registering Built-in Dispatch Tables](#registering-built-in-dispatch-tables)

## Accessing the Addon Manager

Use `Dali::Integration::AddOnManager::Get` to access the active manager instance. In a dali-ui application, keep add-on integration separate from your `Dali::Ui::View` tree: views own application UI, while `Dali::Integration::AddOnManager` handles add-on discovery, loading, procedure lookup, and lifecycle forwarding.

```cpp
void InitializePlatformAddOns()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();

  if(!manager)
  {
    return;
  }

  manager->Start();
}
```

`Dali::Integration::AddOnManager` is the public entry point for this feature. The constructor `Dali::Integration::AddOnManager::AddOnManager` and destructor `Dali::Integration::AddOnManager::~AddOnManager` are part of the manager type, but the manager is a singleton created by the adaptor, so application integration code normally works with the pointer returned by `Dali::Integration::AddOnManager::Get`.

## Discovering and Inspecting Add-ons

Call `Dali::Integration::AddOnManager::EnumerateAddOns` to list add-on names known to the manager. For a specific add-on, `Dali::Integration::AddOnManager::GetAddOnInfo` fills a `Dali::AddOnInfo` record containing `Dali::AddOnInfo::name`, `Dali::AddOnInfo::type`, `Dali::AddOnInfo::version`, `Dali::AddOnInfo::buildInfo`, and `Dali::AddOnInfo::next`.

The nested `Dali::AddOnInfo::BuildInfo` record exposes build compatibility fields through `Dali::AddOnInfo::BuildInfo::libCoreVersion`, `Dali::AddOnInfo::BuildInfo::libAdaptorVersion`, and `Dali::AddOnInfo::BuildInfo::libToolkitVersion`.

```cpp
void PrintAvailableAddOns()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();

  if(!manager)
  {
    return;
  }

  const std::vector<std::string> addOnNames = manager->EnumerateAddOns();

  for(const std::string& addOnName : addOnNames)
  {
    Dali::AddOnInfo info{};

    if(manager->GetAddOnInfo(addOnName, info))
    {
      std::cout << "Add-on: " << info.name << std::endl;
      std::cout << "Type: " << info.type << std::endl;
      std::cout << "Version: " << info.version << std::endl;
      std::cout << "Core: " << info.buildInfo.libCoreVersion << std::endl;
      std::cout << "Adaptor: " << info.buildInfo.libAdaptorVersion << std::endl;
      std::cout << "Toolkit: " << info.buildInfo.libToolkitVersion << std::endl;
    }
  }
}
```

Use `Dali::AddOnInfo::next` only when add-on metadata supplies an additional data structure through that pointer. For most application-level checks, read the current `Dali::AddOnInfo` entry and avoid depending on add-ons that are not present in `Dali::Integration::AddOnManager::EnumerateAddOns`.

## Loading Add-ons and Calling Procedures

Use `Dali::Integration::AddOnManager::LoadAddOn` when you know both the add-on name and library name. Use `Dali::Integration::AddOnManager::LoadAddOns` when you want to load several add-ons that are already known to the manager by name.

```cpp
void LoadRequiredAddOns()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();

  if(!manager)
  {
    return;
  }

  auto imageAddOn = manager->LoadAddOn("image-loader", "libimage-loader-addon.so");

  std::vector<std::string> optionalAddOns;
  optionalAddOns.push_back("metrics");
  optionalAddOns.push_back("diagnostics");

  const auto loadedAddOns = manager->LoadAddOns(optionalAddOns);

  std::cout << "Loaded optional add-ons: " << loadedAddOns.size() << std::endl;
}
```

After an add-on is loaded, use `Dali::Integration::AddOnManager::GetGlobalProc` to retrieve a typed callable for a global exported procedure. The template argument is the function signature you expect from the add-on.

```cpp
void CallGlobalAddOnFunction()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();

  if(!manager)
  {
    return;
  }

  auto addOn = manager->LoadAddOn("diagnostics", "libdiagnostics-addon.so");

  auto getValue = manager->GetGlobalProc<int()>(addOn, "GetDiagnosticsValue");

  if(getValue)
  {
    const int value = getValue();
    std::cout << "Diagnostics value: " << value << std::endl;
  }
}
```

For direct one-shot calls, `Dali::Integration::AddOnManager::InvokeGlobalProc` looks up the exported procedure by name and invokes it with the supplied arguments. Use it when the required add-on handle is valid and the exported procedure is expected to be present.

```cpp
int ReadAddOnCounter()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();

  if(!manager)
  {
    return 0;
  }

  auto addOn = manager->LoadAddOn("counter", "libcounter-addon.so");

  if(!addOn)
  {
    return 0;
  }

  return manager->InvokeGlobalProc<int>(addOn, "ReadCounter");
}
```

Use `Dali::Integration::AddOnManager::GetInstanceProc` when an add-on exposes a procedure that operates on an instance pointer or handle supplied by the add-on integration layer.

```cpp
void ResetAddOnInstance(void* instance)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();

  if(!manager || !instance)
  {
    return;
  }

  auto addOn = manager->LoadAddOn("counter", "libcounter-addon.so");

  auto reset = manager->GetInstanceProc<void(void*)>(addOn, "ResetCounterInstance");

  if(reset)
  {
    reset(instance);
  }
}
```

## Coordinating Add-on Lifecycle

Forward application state changes to the add-on system with `Dali::Integration::AddOnManager::Start`, `Dali::Integration::AddOnManager::Pause`, `Dali::Integration::AddOnManager::Resume`, and `Dali::Integration::AddOnManager::Stop`.

This keeps add-on lifecycle handling aligned with the surrounding dali-ui application while your `Dali::Ui::View` hierarchy remains focused on presentation and interaction.

```cpp
class AddOnLifecycleController
{
public:
  void OnApplicationStarted()
  {
    if(auto* manager = Dali::Integration::AddOnManager::Get())
    {
      manager->Start();
    }
  }

  void OnApplicationPaused()
  {
    if(auto* manager = Dali::Integration::AddOnManager::Get())
    {
      manager->Pause();
    }
  }

  void OnApplicationResumed()
  {
    if(auto* manager = Dali::Integration::AddOnManager::Get())
    {
      manager->Resume();
    }
  }

  void OnApplicationStopped()
  {
    if(auto* manager = Dali::Integration::AddOnManager::Get())
    {
      manager->Stop();
    }
  }
};
```

The lifecycle calls are manager-level operations. Add-ons that provide dispatch tables can respond through `Dali::AddOnDispatchTable::OnStart`, `Dali::AddOnDispatchTable::OnPause`, `Dali::AddOnDispatchTable::OnResume`, and `Dali::AddOnDispatchTable::OnStop`.

## Registering Built-in Dispatch Tables

Some add-on or platform integration code exposes add-ons through a `Dali::AddOnDispatchTable` instead of relying only on symbol lookup from a shared library. Register that table with `Dali::Integration::AddOnManager::RegisterAddOnDispatchTable`.

A `Dali::AddOnDispatchTable` identifies the add-on with `Dali::AddOnDispatchTable::name`, provides metadata through `Dali::AddOnDispatchTable::GetAddOnInfo`, exposes global procedures through `Dali::AddOnDispatchTable::GetGlobalProc`, exposes instance procedures through `Dali::AddOnDispatchTable::GetInstanceProc`, and receives lifecycle callbacks through its `OnStart`, `OnPause`, `OnResume`, and `OnStop` entries.

```cpp
void RegisterBuiltInAddOn(const Dali::AddOnDispatchTable* dispatchTable)
{
  if(!dispatchTable)
  {
    return;
  }

  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();

  if(!manager)
  {
    return;
  }

  manager->RegisterAddOnDispatchTable(dispatchTable);
}
```

Once registered, a built-in dispatch table participates in the same manager workflow as other add-ons: code can discover metadata with `Dali::Integration::AddOnManager::GetAddOnInfo`, obtain callable procedures with `Dali::Integration::AddOnManager::GetGlobalProc` or `Dali::Integration::AddOnManager::GetInstanceProc`, and forward lifecycle events through `Dali::Integration::AddOnManager::Start`, `Pause`, `Resume`, and `Stop`.
