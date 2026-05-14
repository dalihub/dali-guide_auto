---
title: 애드온 매니저
sidebar_label: 애드온 매니저
category: platform-integration
---

# 애드온 매니저

애드온 매니저는 동적으로 로드 가능한 라이브러리로 DALi 애플리케이션을 확장하기 위한 플러그인 아키텍처를 제공합니다. 런타임에 애드온을 발견, 로드 및 통신할 수 있습니다.

## 목차

- [사용 가능한 애드온 발견](#사용-가능한-애드온-발견)
- [애드온 로드 및 사용](#애드온-로드-및-사용)
- [애드온 정보 조회](#애드온-정보-조회)
- [애드온 함수 바인딩](#애드온-함수-바인딩)
- [커스텀 애드온 생성](#커스텀-애드온-생성)
- [애드온 수명 주기 관리](#애드온-수명-주기-관리)

## 사용 가능한 애드온 발견

`Dali::Integration::AddOnManager` 싱글톤이 모든 애드온 작업을 관리합니다. `EnumerateAddOns()`를 사용하여 시스템에서 사용 가능한 애드온 이름 목록을 조회합니다.

```cpp
#include <dali/integration-api/addon-manager.h>

void ListAvailableAddons()
{
    Dali::Integration::AddOnManager* addonManager = Dali::Integration::AddOnManager::Get();
    if(!addonManager)
    {
        // 이 플랫폼에서는 AddonManager를 사용할 수 없음
        return;
    }

    std::vector<std::string> availableAddons = addonManager->EnumerateAddOns();
    for(const auto& name : availableAddons)
    {
        // 각 애드온 이름 처리
    }
}
```

매니저를 사용하기 전에 항상 `Dali::Integration::AddOnManager::Get()`이 유효한 포인터를 반환하는지 확인하세요. 모든 플랫폼에서 애드온 지원이 가능하지 않을 수 있습니다.

## 애드온 로드 및 사용

### 이름으로 애드온 로드

`LoadAddOns()`를 사용하여 이름으로 하나 이상의 애드온을 로드합니다. 이 메서드는 `Dali::AddOnLibrary` 핸들의 벡터를 반환합니다.

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
            // 애드온 로드 성공
        }
    }
}
```

### 특정 라이브러리에서 로드

`LoadAddOn()`을 사용하여 특정 라이브러리 파일 경로에서 애드온을 로드합니다.

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

### 이미 로드된 애드온 가져오기

`GetAddOn()`을 사용하여 이미 로드되었을 수 있는 애드온의 핸들을 조회합니다.

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

## 애드온 정보 조회

`Dali::AddOnInfo` 구조체에는 이름, 버전, 타입, 빌드 정보를 포함한 애드온 메타데이터가 포함되어 있습니다.

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
        // 애드온 메타데이터 접근
        std::string name = info.name;
        uint32_t version = info.version;
        Dali::AddOnType type = info.type;

        // 빌드 정보
        uint32_t coreVersion = info.buildInfo.libCoreVersion;
        uint32_t adaptorVersion = info.buildInfo.libAdaptorVersion;
        uint32_t toolkitVersion = info.buildInfo.libToolkitVersion;
    }
}
```

### 버전 인코딩

`DALI_ADDON_VERSION` 매크로를 사용하여 버전 번호를 32비트 정수로 인코딩합니다.

```cpp
uint32_t version = Dali::DALI_ADDON_VERSION(1, 2, 3);  // Major.Minor.Revision
```

## 애드온 함수 바인딩

### AddOnBinder를 사용한 자동 바인딩

`Dali::AddOn::AddOnBinder` 클래스는 애드온 함수를 C++ 멤버 함수에 바인딩하는 것을 단순화합니다. `ADDON_BIND_FUNCTION` 매크로를 사용하여 바인딩된 함수를 선언합니다.

```cpp
#include <dali/devel-api/common/addon-binder.h>

struct MyImageLoader : public Dali::AddOn::AddOnBinder
{
    MyImageLoader()
    : Dali::AddOn::AddOnBinder("MyImageLoaderAddon")
    {
    }

    // 시그니처와 함께 애드온 함수 바인딩
    ADDON_BIND_FUNCTION(LoadImage, bool(const std::string&, Dali::Devel::PixelBuffer&));
    ADDON_BIND_FUNCTION(GetImageSize, void(unsigned int&, unsigned int&));
};

void UseImageLoader()
{
    Dali::Integration::AddOnManager* addonManager = Dali::Integration::AddOnManager::Get();
    if(!addonManager)
    {
        return;
    }

    Dali::AddOnLibrary handle = addonManager->GetAddOn("MyImageLoaderAddon");
    if(handle)
    {
        auto loadImageFunc = addonManager->GetGlobalProc<bool(const std::string&, Dali::Devel::PixelBuffer&)>(handle, "LoadImage");
        if(loadImageFunc)
        {
            Dali::Devel::PixelBuffer buffer;
            if(loadImageFunc("image.png", buffer))
            {
                // 이미지 로드 성공
            }
        }
    }
}
```

### 수동 함수 조회

더 많은 제어를 위해 `GetGlobalProc()`과 `GetInstanceProc()`을 사용하여 함수 포인터를 직접 조회합니다.

```cpp
#include <dali/integration-api/addon-manager.h>

void UseManualBinding(Dali::AddOnLibrary handle)
{
    Dali::Integration::AddOnManager* addonManager = Dali::Integration::AddOnManager::Get();
    if(!addonManager)
    {
        return;
    }

    // 타입이 있는 전역 함수 가져오기
    auto processFunc = addonManager->GetGlobalProc<int(int, int)>(handle, "ProcessData");
    if(processFunc)
    {
        int result = processFunc(10, 20);
    }

    // 전역 함수 직접 호출
    int result = addonManager->InvokeGlobalProc<int, int, int>(handle, "AddNumbers", 5, 7);
}
```

## 커스텀 애드온 생성

### AddOnBase 구현

`Dali::AddOns::AddOnBase`에서 상속받아 커스텀 애드온을 생성합니다. 필수 순수 가상 메서드를 구현하고 `REGISTER_ADDON_CLASS` 매크로를 사용하여 애드온을 등록합니다.

```cpp
#include <dali/devel-api/addons/addon-base.h>

// 노출할 전역 함수들
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
        return nullptr;  // 인스턴스 함수 없음
    }

    void OnStart() override
    {
        // 애드온 시작 시 호출
    }

    void OnStop() override
    {
        // 애드온 정지 시 호출
    }
};

// 애드온 클래스 등록
REGISTER_ADDON_CLASS(MyCustomAddOn);
```

### 디스패치 테이블 사용

`Dali::AddOns::DispatchTable`은 함수 이름을 함수 포인터에 매핑합니다. 전역 함수는 `GetGlobalDispatchTable()`에서, 인스턴스 메서드는 `GetInstanceDispatchTable()`에서 채웁니다.

```cpp
Dali::AddOns::DispatchTable* GetGlobalDispatchTable() override
{
    static Dali::AddOns::DispatchTable dispatchTable{};
    if(dispatchTable.Empty())
    {
        // 각 함수를 이름으로 등록
        dispatchTable["FunctionA"] = FunctionA;
        dispatchTable["FunctionB"] = FunctionB;
    }
    return &dispatchTable;
}
```

## 애드온 수명 주기 관리

애드온 매니저는 애플리케이션 수명 주기 이벤트를 모든 등록된 애드온에 전파합니다. 어댑터는 애플리케이션 수명 주기 전환 중에 이 메서드들을 자동으로 호출합니다.

### 수명 주기 콜백

애드온은 `Dali::AddOns::AddOnBase`의 가상 메서드를 재정의하여 수명 주기 이벤트에 응답할 수 있습니다:

- `OnStart()` - 애플리케이션이 시작할 때 호출
- `OnResume()` - 애플리케이션이 백그라운드에서 재개될 때 호출
- `OnPause()` - 애플리케이션이 백그라운드로 전환될 때 호출
- `OnStop()` - 애플리케이션이 정지할 때 호출

```cpp
class LifecycleAwareAddOn : public Dali::AddOns::AddOnBase
{
public:
    void OnStart() override
    {
        // 리소스 초기화
    }

    void OnResume() override
    {
        // 작업 재개
    }

    void OnPause() override
    {
        // 상태 저장, 리소스 해제
    }

    void OnStop() override
    {
        // 정리
    }

    // ... 기타 필수 메서드들
};
```

### 매니저 수명 주기 메서드

`Dali::Integration::AddOnManager`는 모든 로드된 애드온에 이벤트를 전파하는 수명 주기 메서드를 제공합니다:

```cpp
// 애플리케이션 수명 주기 동안 어댑터에 의해 호출됨
addonManager->Start();   // 모든 애드온에 OnStart 전파
addonManager->Resume();  // 모든 애드온에 OnResume 전파
addonManager->Pause();    // 모든 애드온에 OnPause 전파
addonManager->Stop();    // 모든 애드온에 OnStop 전파
```

### 디스패치 테이블 등록

애드온은 `Dali::AddOnDispatchTable` 포인터로 `RegisterAddOnDispatchTable()`을 호출하여 자체 등록합니다. `REGISTER_ADDON_CLASS` 매크로가 이를 자동으로 처리합니다.

```cpp
// 수동 등록 (일반적으로 REGISTER_ADDON_CLASS 매크로가 처리)
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
```
