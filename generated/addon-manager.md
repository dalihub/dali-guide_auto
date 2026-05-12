---
title: Addon Manager
sidebar_label: Addon Manager
category: platform-integration
---

# Addon Manager

`Dali::Integration::AddOnManager` loads platform add-ons, queries their metadata, retrieves exported procedures, and forwards application lifecycle events to loaded add-ons.

## Table of Contents

- [Getting the Add-on Manager](#getting-the-add-on-manager)
- [Discovering and Loading Add-ons](#discovering-and-loading-add-ons)
- [Reading Add-on Metadata](#reading-add-on-metadata)
- [Calling Add-on Procedures](#calling-add-on-procedures)
- [Forwarding Application Lifecycle](#forwarding-application-lifecycle)
- [Registering a Dispatch Table](#registering-a-dispatch-table)

## Getting the Add-on Manager

In a dali-ui application, your UI should remain built around `Dali::Ui::View`. `Dali::Integration::AddOnManager` is used at the platform-integration boundary, typically from application setup code that prepares optional platform functionality before views depend on it.

Use `Dali::Integration::AddOnManager::Get` to access the process-wide manager. Check the returned pointer before using it, because add-on support is integration-provided.

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

`Dali::Integration::AddOnManager::AddOnManager` and `Dali::Integration::AddOnManager::~AddOnManager` belong to the integration implementation. Application code normally uses `Dali::Integration::AddOnManager::Get` rather than constructing a manager directly.

## Discovering and Loading Add-ons

Use `Dali::Integration::AddOnManager::EnumerateAddOns` to obtain the add-on names visible to the integration layer. Load one add-on with `Dali::Integration::AddOnManager::LoadAddOn`, or load a selected list with `Dali::Integration::AddOnManager::LoadAddOns`.

```cpp
void LoadAvailableAddOns()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return;
  }

  const std::vector<std::string> names = manager->EnumerateAddOns();

  for(const std::string& name : names)
  {
    auto addOn = manager->LoadAddOn(name, name);
    (void)addOn;
  }
}
```

When the application already knows which add-ons it requires, pass those names directly to `Dali::Integration::AddOnManager::LoadAddOns`.

```cpp
void LoadRequiredAddOns()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return;
  }

  const std::vector<std::string> requiredAddOns =
  {
    "image-loader",
    "platform-service"
  };

  const auto loadedAddOns = manager->LoadAddOns(requiredAddOns);
  (void)loadedAddOns;
}
```

Use `Dali::Integration::AddOnManager::GetAddOn` when the add-on has already been loaded and you need its library handle for procedure lookup.

```cpp
auto FindLoadedAddOn(const std::string& addOnName)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return decltype(manager->GetAddOn(addOnName)){};
  }

  return manager->GetAddOn(addOnName);
}
```

## Reading Add-on Metadata

`Dali::AddOnInfo` describes one add-on. `Dali::Integration::AddOnManager::GetAddOnInfo` fills a `Dali::AddOnInfo` instance for a named add-on and returns whether information was found.

The primary fields are `Dali::AddOnInfo::name`, `Dali::AddOnInfo::type`, `Dali::AddOnInfo::version`, `Dali::AddOnInfo::buildInfo`, and `Dali::AddOnInfo::next`. Build compatibility information is grouped under `Dali::AddOnInfo::BuildInfo`, including `Dali::AddOnInfo::BuildInfo::libCoreVersion`, `Dali::AddOnInfo::BuildInfo::libAdaptorVersion`, and `Dali::AddOnInfo::BuildInfo::libToolkitVersion`.

```cpp
bool IsAddOnKnown(const std::string& addOnName)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return false;
  }

  Dali::AddOnInfo info;
  if(!manager->GetAddOnInfo(addOnName, info))
  {
    return false;
  }

  const char* name           = info.name;
  const char* type           = info.type;
  const char* version        = info.version;
  const char* coreVersion    = info.buildInfo.libCoreVersion;
  const char* adaptorVersion = info.buildInfo.libAdaptorVersion;
  const char* toolkitVersion = info.buildInfo.libToolkitVersion;

  (void)name;
  (void)type;
  (void)version;
  (void)coreVersion;
  (void)adaptorVersion;
  (void)toolkitVersion;

  return true;
}
```

`Dali::AddOnInfo::next` allows add-on metadata to be represented as a linked list when an add-on exposes more than one information record.

```cpp
void VisitAddOnInfoChain(const Dali::AddOnInfo& firstInfo)
{
  const Dali::AddOnInfo* current = &firstInfo;

  while(current)
  {
    const char* name    = current->name;
    const char* version = current->version;

    (void)name;
    (void)version;

    current = current->next;
  }
}
```

## Calling Add-on Procedures

After an add-on is loaded, use `Dali::Integration::AddOnManager::GetGlobalProc` to retrieve a typed global procedure. The template argument is the function signature you expect from the add-on.

```cpp
bool TryReadAddOnStatus(const std::string& addOnName)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return false;
  }

  auto addOn = manager->GetAddOn(addOnName);
  auto getStatus = manager->GetGlobalProc<int()>(addOn, "GetStatus");

  if(!getStatus)
  {
    return false;
  }

  const int status = getStatus();
  return status == 0;
}
```

For one-off calls, `Dali::Integration::AddOnManager::InvokeGlobalProc` combines lookup and invocation.

```cpp
int ReadAddOnValue(const std::string& addOnName, int fallbackValue)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return fallbackValue;
  }

  auto addOn = manager->GetAddOn(addOnName);
  return manager->InvokeGlobalProc<int>(addOn, "ReadValue", fallbackValue);
}
```

Use `Dali::Integration::AddOnManager::GetInstanceProc` when the exported function is associated with an add-on instance object supplied by that add-on.

```cpp
bool ConfigureAddOnInstance(const std::string& addOnName, void* instance, int option)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return false;
  }

  auto addOn = manager->GetAddOn(addOnName);
  auto configure = manager->GetInstanceProc<bool(void*, int)>(addOn, "Configure");

  if(!configure)
  {
    return false;
  }

  return configure(instance, option);
}
```

## Forwarding Application Lifecycle

Add-ons that allocate platform resources should receive lifecycle transitions. Call `Dali::Integration::AddOnManager::Start` when add-on services should become active, `Dali::Integration::AddOnManager::Pause` when the application is suspended, `Dali::Integration::AddOnManager::Resume` when it becomes active again, and `Dali::Integration::AddOnManager::Stop` during shutdown.

```cpp
class AddOnLifecycle
{
public:
  void OnAppStart()
  {
    if(Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get())
    {
      manager->Start();
    }
  }

  void OnAppPause()
  {
    if(Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get())
    {
      manager->Pause();
    }
  }

  void OnAppResume()
  {
    if(Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get())
    {
      manager->Resume();
    }
  }

  void OnAppStop()
  {
    if(Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get())
    {
      manager->Stop();
    }
  }
};
```

The manager forwards these transitions through each registered `Dali::AddOnDispatchTable` by using lifecycle entries such as `Dali::AddOnDispatchTable::OnStart`, `Dali::AddOnDispatchTable::OnPause`, `Dali::AddOnDispatchTable::OnResume`, and `Dali::AddOnDispatchTable::OnStop`.

## Registering a Dispatch Table

`Dali::AddOnDispatchTable` is the add-on side of the integration contract. It identifies an add-on through `Dali::AddOnDispatchTable::name`, exposes metadata through `Dali::AddOnDispatchTable::GetAddOnInfo`, and provides procedure lookup through `Dali::AddOnDispatchTable::GetGlobalProc` and `Dali::AddOnDispatchTable::GetInstanceProc`.

Application developers usually consume add-ons through `Dali::Integration::AddOnManager`; platform or add-on integration code registers dispatch tables with `Dali::Integration::AddOnManager::RegisterAddOnDispatchTable`.

```cpp
extern const Dali::AddOnDispatchTable gExampleAddOnDispatchTable;

void RegisterExampleAddOn()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return;
  }

  manager->RegisterAddOnDispatchTable(&gExampleAddOnDispatchTable);
}
```

Once registered, the manager can use the dispatch table to answer `Dali::Integration::AddOnManager::GetAddOnInfo`, resolve procedures through `Dali::Integration::AddOnManager::GetGlobalProc`, and include the add-on in lifecycle dispatch through `Dali::Integration::AddOnManager::Start`, `Dali::Integration::AddOnManager::Pause`, `Dali::Integration::AddOnManager::Resume`, and `Dali::Integration::AddOnManager::Stop`.
