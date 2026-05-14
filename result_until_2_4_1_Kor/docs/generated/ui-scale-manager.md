---
title: UI 스케일 관리자
sidebar_label: UI 스케일 관리자
category: styling-theme-config
---

# UI 스케일 관리자 {#ui-scale-manager}

`Dali::Ui::UiScaleManager`는 dali-ui `Dali::Ui::View` 레이아웃 트리에서 사용하는 시스템 기반 UI 스케일에 접근할 수 있게 해줍니다.

## 목차 {#table-of-contents}

- [애플리케이션 UI 스케일 설정](#setting-the-application-ui-scale)
- [현재 스케일 읽기 및 재사용](#reading-and-reusing-the-current-scale)
- [사용자 또는 시스템 스케일 프리셋 적용](#applying-user-or-system-scale-presets)
- [스케일 관리자 핸들 사용](#working-with-scale-manager-handles)

## 애플리케이션 UI 스케일 설정 {#setting-the-application-ui-scale}

`Dali::Ui::UiScaleManager::Get`을 사용해 싱글턴 스케일 관리자에 접근한 다음, 등록된 dali-ui 레이아웃 루트가 사용할 스케일 배율을 `Dali::Ui::UiScaleManager::SetScale`에 전달해 호출합니다.

애플리케이션이 직접 다루는 모델은 여전히 `Dali::Ui::View` 트리입니다. `Dali::Ui::UiScaleManager`는 등록된 dali-ui 레이아웃 루트가 레이아웃을 다시 계산할 때 사용하는 중앙 스케일 소스입니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ConfigureApplicationScale(float scale)
{
  Dali::Ui::UiScaleManager scaleManager = Dali::Ui::UiScaleManager::Get();
  scaleManager.SetScale(scale);
}
```

한 번만 설정하는 경우에는 싱글턴 핸들에서 `Dali::Ui::UiScaleManager::SetScale`을 직접 호출하면 간결합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void UseLargeUiScale()
{
  Dali::Ui::UiScaleManager::Get().SetScale(1.5f);
}
```

`Dali::Ui::UiScaleManager::SetScale`은 `NaN`, 0, 음수 값을 무시합니다. 애플리케이션 동작에 실제로 반영하려면 양수 스케일 값을 전달해야 합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyValidatedScale(float requestedScale)
{
  if(requestedScale > 0.0f)
  {
    Dali::Ui::UiScaleManager::Get().SetScale(requestedScale);
  }
}
```

## 현재 스케일 읽기 및 재사용 {#reading-and-reusing-the-current-scale}

애플리케이션 로직에서 현재 UI 스케일을 표시, 저장, 비교 또는 복원해야 할 때는 `Dali::Ui::UiScaleManager::GetScale`을 호출합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

float ReadApplicationScale()
{
  Dali::Ui::UiScaleManager scaleManager = Dali::Ui::UiScaleManager::Get();
  return scaleManager.GetScale();
}
```

다른 스케일을 임시로 적용하기 전에 이전 값을 보관하는 방식이 자주 사용됩니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

float ApplyTemporaryScale(float temporaryScale)
{
  Dali::Ui::UiScaleManager scaleManager = Dali::Ui::UiScaleManager::Get();

  float previousScale = scaleManager.GetScale();
  scaleManager.SetScale(temporaryScale);

  return previousScale;
}

void RestoreScale(float savedScale)
{
  Dali::Ui::UiScaleManager::Get().SetScale(savedScale);
}
```

`1.0f` 같은 가정을 캐시하지 말고 `Dali::Ui::UiScaleManager::GetScale`을 사용하십시오. 기본 스케일은 `1.0f`이지만, 현재 값은 이미 애플리케이션 또는 플랫폼의 스케일 처리에 의해 변경되었을 수 있습니다.

## 사용자 또는 시스템 스케일 프리셋 적용 {#applying-user-or-system-scale-presets}

설정 화면, 접근성 환경설정 또는 테스트 제어 기능이 있는 dali-ui 애플리케이션에서는 선택된 값을 `Dali::Ui::UiScaleManager::SetScale`에 전달할 값으로 매핑합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

enum class UiScalePreset
{
  Compact,
  Normal,
  Comfortable,
  Large,
  ExtraLarge
};

float ToScaleFactor(UiScalePreset preset)
{
  switch(preset)
  {
    case UiScalePreset::Compact:
    {
      return 0.8f;
    }
    case UiScalePreset::Normal:
    {
      return 1.0f;
    }
    case UiScalePreset::Comfortable:
    {
      return 1.2f;
    }
    case UiScalePreset::Large:
    {
      return 1.5f;
    }
    case UiScalePreset::ExtraLarge:
    {
      return 2.0f;
    }
  }

  return 1.0f;
}

void ApplyScalePreset(UiScalePreset preset)
{
  Dali::Ui::UiScaleManager::Get().SetScale(ToScaleFactor(preset));
}
```

사용자 입력에서 사용자 지정 값이 들어오는 경우에는 `Dali::Ui::UiScaleManager::SetScale`에 전달하기 전에 먼저 검증합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyCustomScale(float customScale)
{
  const bool withinApplicationRange = customScale >= 0.8f && customScale <= 2.0f;

  if(withinApplicationRange)
  {
    Dali::Ui::UiScaleManager::Get().SetScale(customScale);
  }
}
```

`Dali::Ui::UiScaleManager::SetScale` 호출이 성공하면 dali-ui는 등록된 레이아웃 루트의 유효 스케일 캐시를 재설정하고 측정 상태를 무효화합니다. 일반적으로 애플리케이션 코드는 스케일 관리자를 업데이트한 뒤, `Dali::Ui::View` 트리가 새 유효 스케일로 측정과 배치를 수행하도록 두면 됩니다.

## 스케일 관리자 핸들 사용 {#working-with-scale-manager-handles}

`Dali::Ui::UiScaleManager`는 핸들 타입입니다. `Dali::Ui::UiScaleManager::Get`이 반환한 핸들을 저장하거나, 복사하거나, 헬퍼 함수에 전달하거나, `Dali::Ui::UiScaleManager::UiScaleManager`와 `Dali::Ui::UiScaleManager::operator=`를 사용해 이동할 수 있습니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

class ScaleController
{
public:
  ScaleController()
  : mScaleManager(Dali::Ui::UiScaleManager::Get())
  {
  }

  void SetScale(float scale)
  {
    mScaleManager.SetScale(scale);
  }

  float GetScale() const
  {
    return mScaleManager.GetScale();
  }

private:
  Dali::Ui::UiScaleManager mScaleManager;
};
```

저장된 핸들 값을 헬퍼 객체 사이에서 넘길 때는 이동 생성 또는 이동 대입을 사용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

#include <utility>

Dali::Ui::UiScaleManager CreateScaleManagerHandle()
{
  return Dali::Ui::UiScaleManager::Get();
}

void ReplaceScaleManagerHandle()
{
  Dali::Ui::UiScaleManager scaleManager = CreateScaleManagerHandle();

  Dali::Ui::UiScaleManager replacement = Dali::Ui::UiScaleManager::Get();
  scaleManager = std::move(replacement);

  scaleManager.SetScale(1.2f);
}
```

코드가 일반 DALi 핸들을 받은 뒤 `Dali::Ui::UiScaleManager` 핸들을 복구해야 할 때는 `Dali::Ui::UiScaleManager::DownCast`를 사용할 수 있습니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::UiScaleManager RecoverScaleManager(Dali::BaseHandle handle)
{
  return Dali::Ui::UiScaleManager::DownCast(handle);
}
```

`Dali::Ui::UiScaleManager::~UiScaleManager`는 기본 핸들 소멸자입니다. 애플리케이션 코드에서 싱글턴 스케일 관리자 핸들을 명시적으로 정리할 필요는 없습니다.
