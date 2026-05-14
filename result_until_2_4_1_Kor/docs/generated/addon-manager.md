---
title: 애드온 관리자
sidebar_label: 애드온 관리자
category: platform-integration
---

# 애드온 관리자 {#addon-manager}

애드온 관리자는 DALi 애플리케이션이 `Dali::Integration::AddOnManager`를 통해 플랫폼에서 제공하는 애드온 라이브러리를 검색하고, 검사하고, 로드하고, 호출할 수 있게 해 줍니다.

## 목차 {#table-of-contents}

- [dali-ui 앱에서 애드온 관리자의 위치](#where-addon-manager-fits-in-a-dali-ui-app)
- [사용 가능한 애드온 검색](#discovering-available-add-ons)
- [애드온 메타데이터 읽기](#reading-add-on-metadata)
- [애드온 로드 및 전역 프로시저 호출](#loading-an-add-on-and-calling-global-procedures)
- [명시적 라이브러리 로드 사용](#using-explicit-library-loading)
- [생명주기 전달](#lifecycle-forwarding)

## dali-ui 앱에서 애드온 관리자의 위치 {#where-addon-manager-fits-in-a-dali-ui-app}

dali-ui 애플리케이션은 사용자 인터페이스를 `Dali::Ui::View` 트리 안에 유지하고, 선택 기능을 로드하거나 애드온 프로시저를 확인하는 등의 플랫폼 통합 작업에만 애드온 관리자를 사용해야 합니다. 앱에서 다루는 UI 객체 모델은 View 기반으로 유지되며, `Dali::Integration::AddOnManager`는 싱글턴에서 가져오는 서비스 객체입니다.

```cpp
#include <dali/integration-api/addon-manager.h>

void UseAddOnsFromViewCode()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return;
  }

  const std::vector<std::string> addOnNames = manager->EnumerateAddOns();
  for(const std::string& name : addOnNames)
  {
    // Use add-on availability to decide which dali-ui View behavior to enable.
  }
}
```

`Dali::Integration::AddOnManager::Get()`은 어댑터가 생성한 관리자를 반환합니다. 플랫폼이나 테스트 환경에 애드온 관리자 구현이 설치되어 있지 않으면 반환된 포인터가 `nullptr`일 수 있습니다.

## 사용 가능한 애드온 검색 {#discovering-available-add-ons}

`Dali::Integration::AddOnManager::EnumerateAddOns()`를 사용하면 플랫폼 구현에 사용 가능한 애드온 이름을 요청할 수 있습니다. 결과는 `std::vector<std::string>`이며, 여기에 포함된 이름은 `Dali::Integration::AddOnManager::GetAddOnInfo()`, `Dali::Integration::AddOnManager::LoadAddOns()` 또는 `Dali::Integration::AddOnManager::GetAddOn()`에 전달할 수 있습니다.

```cpp
#include <dali/integration-api/addon-manager.h>

std::vector<std::string> GetAvailableAddOns()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return {};
  }

  return manager->EnumerateAddOns();
}
```

dali-ui 앱에서는 일반적으로 기능을 설정하는 단계에서 검색을 수행합니다. 그런 다음 UI는 사용 가능한 애드온이 지원하는 동작만 노출할 수 있으며, 시각적 계층은 계속 `Dali::Ui::View`를 기반으로 유지됩니다.

## 애드온 메타데이터 읽기 {#reading-add-on-metadata}

`Dali::AddOnInfo`는 애드온을 설명합니다. `Dali::Integration::AddOnManager::GetAddOnInfo()`는 호출자가 제공한 `Dali::AddOnInfo`를 채우고, 정보를 가져오면 `true`를 반환합니다.

메타데이터 필드는 `Dali::AddOnInfo`에 저장됩니다.

- `Dali::AddOnInfo::type`
- `Dali::AddOnInfo::next`
- `Dali::AddOnInfo::name`
- `Dali::AddOnInfo::version`
- `Dali::AddOnInfo::buildInfo`

빌드 버전 필드는 `Dali::AddOnInfo::BuildInfo` 아래에 묶여 있습니다.

- `Dali::AddOnInfo::BuildInfo::libCoreVersion`
- `Dali::AddOnInfo::BuildInfo::libAdaptorVersion`
- `Dali::AddOnInfo::BuildInfo::libToolkitVersion`

```cpp
#include <dali/integration-api/addon-manager.h>

bool ReadAddOnInfo(const std::string& addOnName, Dali::AddOnInfo& outInfo)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return false;
  }

  return manager->GetAddOnInfo(addOnName, outInfo);
}

void InspectAddOn(const std::string& addOnName)
{
  Dali::AddOnInfo info{};
  if(!ReadAddOnInfo(addOnName, info))
  {
    return;
  }

  const std::string name = info.name;
  const uint32_t version = info.version;
  const uint32_t coreVersion = info.buildInfo.libCoreVersion;
  const uint32_t adaptorVersion = info.buildInfo.libAdaptorVersion;
  const uint32_t toolkitVersion = info.buildInfo.libToolkitVersion;

  (void)name;
  (void)version;
  (void)coreVersion;
  (void)adaptorVersion;
  (void)toolkitVersion;
}
```

`Dali::AddOnInfo::next`는 애드온 계약에서 해당 포인터 뒤에 추가 데이터가 있다고 문서화한 경우에만 사용하세요. 일반적인 애플리케이션 검사에서는 `Dali::AddOnInfo::type`, `Dali::AddOnInfo::name`, `Dali::AddOnInfo::version`, `Dali::AddOnInfo::buildInfo`가 실제로 사용하는 필드입니다.

## 애드온 로드 및 전역 프로시저 호출 {#loading-an-add-on-and-calling-global-procedures}

`Dali::Integration::AddOnManager::GetAddOn()`은 이름으로 애드온을 로드하고 불투명한 `Dali::AddOnLibrary` 핸들을 반환합니다. 이 핸들은 이후 `Dali::Integration::AddOnManager::GetGlobalProc()` 또는 `Dali::Integration::AddOnManager::InvokeGlobalProc()`와 함께 사용합니다.

형식이 지정된 `Dali::Integration::AddOnManager::GetGlobalProc()` 오버로드는 `std::function<T>`를 반환합니다. 프로시저 이름과 시그니처는 애드온 계약과 일치해야 합니다.

```cpp
#include <dali/integration-api/addon-manager.h>

int CallAddOnSum()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return 0;
  }

  auto addOn = manager->GetAddOn("SampleAddOn");
  if(!addOn)
  {
    return 0;
  }

  std::function<int(int, int)> doSum = manager->GetGlobalProc<int(int, int)>(addOn, "DoSum");
  if(!doSum)
  {
    return 0;
  }

  return doSum(3, 4);
}
```

직접 한 번만 호출하려면 `Dali::Integration::AddOnManager::InvokeGlobalProc()`가 지정한 전역 프로시저를 확인한 뒤 요청한 반환 형식으로 호출합니다. 애드온 핸들이 유효하고 해당 프로시저가 존재한다고 알려진 경우에만 사용하세요.

```cpp
#include <dali/integration-api/addon-manager.h>

int InvokeAddOnSum()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return 0;
  }

  auto addOn = manager->GetAddOn("SampleAddOn");
  if(!addOn)
  {
    return 0;
  }

  return manager->InvokeGlobalProc<int>(addOn, "DoSum", 3, 4);
}
```

애플리케이션이 프로시저의 존재 여부를 확인해야 하거나 같은 프로시저를 반복해서 호출한다면 형식이 지정된 `Dali::Integration::AddOnManager::GetGlobalProc()` 형태를 사용하세요. 호출이 단독으로 이루어지고 애드온 계약을 이미 알고 있다면 `Dali::Integration::AddOnManager::InvokeGlobalProc()`를 사용하세요.

## 명시적 라이브러리 로드 사용 {#using-explicit-library-loading}

`Dali::Integration::AddOnManager::LoadAddOn()`은 지정한 라이브러리 이름에서 이름이 지정된 애드온을 로드합니다. 애플리케이션이나 플랫폼 통합 계층이 라이브러리 경로 또는 패키지가 제공하는 라이브러리 이름을 알고 있을 때 유용합니다.

```cpp
#include <dali/integration-api/addon-manager.h>

bool LoadNamedAddOnFromLibrary(const std::string& addOnName, const std::string& libraryName)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return false;
  }

  auto addOn = manager->LoadAddOn(addOnName, libraryName);
  return addOn != nullptr;
}
```

여러 애드온을 이름으로 로드하려면 `Dali::Integration::AddOnManager::LoadAddOns()`를 사용하세요. 반환된 벡터에는 요청한 각 이름에 대응하는 불투명한 `Dali::AddOnLibrary` 핸들이 하나씩 들어 있습니다.

```cpp
#include <dali/integration-api/addon-manager.h>

std::vector<Dali::AddOnLibrary> LoadOptionalAddOns()
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return {};
  }

  return manager->LoadAddOns({"SampleAddOn"});
}
```

로드한 뒤에는 애드온 계약에 따라 null이 아닌 각 핸들을 `Dali::Integration::AddOnManager::GetGlobalProc()` 또는 `Dali::Integration::AddOnManager::GetInstanceProc()`와 함께 사용하세요.

## 생명주기 전달 {#lifecycle-forwarding}

`Dali::Integration::AddOnManager::Start()`, `Dali::Integration::AddOnManager::Resume()`, `Dali::Integration::AddOnManager::Pause()`, `Dali::Integration::AddOnManager::Stop()`은 `Dali::AddOnDispatchTable`을 통해 해당 콜백을 등록한 로드된 애드온에 생명주기 이벤트를 전달합니다.

일반적으로 애플리케이션은 어댑터가 생명주기 전달을 담당하게 둡니다. 통합 계층에서 애드온 생명주기 상태를 명시적으로 조율한다면 관리자 싱글턴에서 이 메서드들을 호출하세요.

```cpp
#include <dali/integration-api/addon-manager.h>

void ForwardAddOnLifecycle(bool visible)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager)
  {
    return;
  }

  if(visible)
  {
    manager->Start();
    manager->Resume();
  }
  else
  {
    manager->Pause();
    manager->Stop();
  }
}
```

`Dali::AddOnDispatchTable`은 애드온 측의 등록 계약입니다. 이 구조체의 필드는 애드온 이름, 메타데이터 콜백, 프로시저 조회 콜백, 생명주기 콜백을 연결합니다.

```cpp
#include <dali/integration-api/addon-manager.h>

void RegisterDispatchTable(const Dali::AddOnDispatchTable* dispatchTable)
{
  Dali::Integration::AddOnManager* manager = Dali::Integration::AddOnManager::Get();
  if(!manager || !dispatchTable)
  {
    return;
  }

  manager->RegisterAddOnDispatchTable(dispatchTable);
}
```

`Dali::AddOnDispatchTable::name`은 애드온을 식별합니다. `Dali::AddOnDispatchTable::GetAddOnInfo`, `Dali::AddOnDispatchTable::GetGlobalProc`, `Dali::AddOnDispatchTable::GetInstanceProc`는 메타데이터와 함수 조회 기능을 제공합니다. `Dali::AddOnDispatchTable::OnStart`, `Dali::AddOnDispatchTable::OnResume`, `Dali::AddOnDispatchTable::OnPause`, `Dali::AddOnDispatchTable::OnStop`은 관리자가 생명주기 변경을 전달할 때 사용하는 선택적 생명주기 콜백입니다.
