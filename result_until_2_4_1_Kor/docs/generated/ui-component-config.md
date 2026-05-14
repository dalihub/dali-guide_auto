---
title: UI 컴포넌트 구성
sidebar_label: UI 컴포넌트 구성
category: styling-theme-config
---

# UI 컴포넌트 구성 {#uicomponentconfig}

`Dali::Ui::UiComponentConfig`는 dali-ui 애플리케이션에서 컴포넌트 기본값을 설정하는 dali-ui-components 구성 핸들입니다.

## 목차 {#table-of-contents}

- [컴포넌트 구성 핸들 생성](#create-the-component-configuration-handle)
- [애플리케이션 시작 시 구성 설정 공유](#share-configuration-setup-from-application-startup)
- [타입이 지정된 컴포넌트 구성 핸들 복구](#recover-a-typed-component-configuration-handle)
- [생성된 체이닝 지원](#generated-chaining-support)

## 컴포넌트 구성 핸들 생성 {#create-the-component-configuration-handle}

`Dali::Ui::UiComponentConfig::New()`를 사용해 `Dali::Ui::UiComponentConfig`를 생성합니다. 반환된 핸들은 `Dali::Ui::UiConfig`에서 상속된 컴포넌트 구성 기본값으로 초기화됩니다. 예를 들어 `scalingFactor=1.0f`, `dpi=160`, `baselineDpi=160`, `defaultFontSize=16.0f`, 검은색 기본 텍스트 색상이 적용됩니다.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig CreateComponentConfig()
{
  return Dali::Ui::UiComponentConfig::New();
}
```

애플리케이션에서 dali-ui-components를 사용할 때는 `Dali::Ui::UiComponentConfig`를 애플리케이션 시작 구성으로 사용합니다. dali-ui 단위나 컴포넌트 기본값에 의존하는 UI를 만들기 전에 적용하고, UI 구조에 대한 애플리케이션 측 객체 모델로는 `Dali::Ui::View`를 유지합니다.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

void ConfigureDaliUiComponents()
{
  Dali::Ui::UiComponentConfig::New()
    .SetDpi(320)
    .SetScalingFactor(1.5f)
    .Apply();
}
```

## 애플리케이션 시작 시 구성 설정 공유 {#share-configuration-setup-from-application-startup}

`Dali::Ui::UiComponentConfig`는 핸들 타입입니다. `Dali::Ui::UiComponentConfig::~UiComponentConfig`를 통한 소멸, `Dali::Ui::UiComponentConfig::UiComponentConfig`를 통한 복사 및 이동 생성, `Dali::Ui::UiComponentConfig::operator=`를 통한 복사 및 이동 대입을 포함해 일반적인 핸들 수명 주기를 지원합니다.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig BuildStartupComponentConfig()
{
  Dali::Ui::UiComponentConfig config =
    Dali::Ui::UiComponentConfig::New();

  return config;
}
```

시작 헬퍼 사이에서 소유권을 전달해야 하는 경우, `Apply()`가 구성 값을 고정하기 전에 핸들을 명시적으로 이동합니다.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

#include <utility>

void ReplaceComponentConfig(Dali::Ui::UiComponentConfig& target)
{
  Dali::Ui::UiComponentConfig replacement =
    Dali::Ui::UiComponentConfig::New();

  target = std::move(replacement);
}
```

## 타입이 지정된 컴포넌트 구성 핸들 복구 {#recover-a-typed-component-configuration-handle}

핸들이 `Dali::BaseHandle`로만 알려져 있고 애플리케이션 코드에서 타입이 지정된 `Dali::Ui::UiComponentConfig` 핸들이 다시 필요할 때는 `Dali::Ui::UiComponentConfig::DownCast`를 사용합니다.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig RecoverComponentConfig(
  Dali::BaseHandle handle)
{
  return Dali::Ui::UiComponentConfig::DownCast(handle);
}
```

제공된 핸들이 컴포넌트 구성 객체를 참조하면 `Dali::Ui::UiComponentConfig::DownCast`는 타입이 지정된 `Dali::Ui::UiComponentConfig`를 반환합니다. 제공된 핸들이 해당 객체 타입을 참조하지 않으면 반환된 핸들은 초기화되지 않은 상태입니다.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

void UseTypedComponentConfig(Dali::BaseHandle source)
{
  Dali::Ui::UiComponentConfig typed =
    Dali::Ui::UiComponentConfig::DownCast(source);

  (void)typed;
}
```

## 생성된 체이닝 지원 {#generated-chaining-support}

`ui-component-config.autogen.h`는 공개 `Dali::Ui::UiComponentConfig` 헤더에 포함됩니다. 이 파일은 `DALI_UI_CHAIN_UICOMPONENTCONFIG_METHODS`를 정의하며, `Dali::Ui::UiComponentConfig`는 `Dali::Ui::UiConfig`에서 상속된 생성형 플루언트 setter를 노출합니다. 예를 들면 `SetDpi()`와 `SetScalingFactor()`가 있습니다.

애플리케이션 코드는 일반적으로 `<dali-ui-components/public-api/ui-component-config.h>`를 포함하고 `Dali::Ui::UiComponentConfig::New()`를 직접 사용합니다. `Apply()`를 호출하기 전에 값을 구성합니다. `Apply()` 이후에는 구성이 고정되므로 setter를 호출하면 안 됩니다.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>

Dali::Ui::UiComponentConfig MakeConfigForViewSetup()
{
  return Dali::Ui::UiComponentConfig::New()
    .SetDpi(320)
    .SetScalingFactor(1.5f);
}
```
