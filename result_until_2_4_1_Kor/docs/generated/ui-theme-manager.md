---
title: UI 테마 관리자
sidebar_label: UI 테마 관리자
category: styling-theme-config
---

# UI 테마 관리자 {#ui-theme-manager}

`Dali::Ui::UiThemeManager`를 사용하면 dali-ui 애플리케이션에서 현재 테마 식별자를 읽고 테마 변경에 대응할 수 있습니다.

## 목차 {#table-of-contents}

- [테마 관리자 접근](#accessing-the-theme-manager)
- [현재 테마 읽기](#reading-the-current-theme)
- [테마 변경에 대응](#reacting-to-theme-changes)
- [뷰 중심 애플리케이션 계층에서 테마 상태 사용](#using-theme-state-from-a-view-oriented-app-layer)

## 테마 관리자 접근 {#accessing-the-theme-manager}

`Dali::Ui::UiThemeManager`는 핸들 기반 싱글턴입니다. 애플리케이션 코드는 `Dali::Ui::UiThemeManager::Get()`으로 싱글턴 핸들을 가져오고, 반환된 핸들은 필요한 동안만 유지합니다.

```cpp
#include <dali-ui-foundation/public-api/ui-theme-manager.h>

class ThemeController
{
public:
  ThemeController()
  : mThemeManager(Dali::Ui::UiThemeManager::Get())
  {
  }

private:
  Dali::Ui::UiThemeManager mThemeManager;
};
```

인프라 코드에서 일반 핸들을 전달받는 경우, 해당 객체가 실제로 테마 관리자라면 `Dali::Ui::UiThemeManager::DownCast()`로 `Dali::Ui::UiThemeManager` 핸들을 복원할 수 있습니다. 핸들이 테마 관리자가 아니면 결과는 초기화되지 않은 핸들입니다.

```cpp
#include <dali/public-api/object/base-handle.h>
#include <dali-ui-foundation/public-api/ui-theme-manager.h>

Dali::Ui::UiThemeManager AsThemeManager(Dali::BaseHandle handle)
{
  return Dali::Ui::UiThemeManager::DownCast(handle);
}
```

## 현재 테마 읽기 {#reading-the-current-theme}

애플리케이션 로직에서 현재 어떤 테마가 사용 중인지 알아야 할 때는 `Dali::Ui::UiThemeManager::GetCurrentThemeId()`를 사용합니다. 반환되는 `Dali::String`은 테마 식별자이며, 활성 테마가 없으면 비어 있을 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/ui-theme-manager.h>

auto ReadThemeId()
{
  Dali::Ui::UiThemeManager themeManager = Dali::Ui::UiThemeManager::Get();
  return themeManager.GetCurrentThemeId();
}
```

일회성 확인처럼 짧게 사용하는 경우에는 싱글턴에 직접 질의해도 됩니다.

```cpp
#include <dali-ui-foundation/public-api/ui-theme-manager.h>

auto currentThemeId = Dali::Ui::UiThemeManager::Get().GetCurrentThemeId();
```

## 테마 변경에 대응 {#reacting-to-theme-changes}

`Dali::Ui::UiThemeManager::ThemeChangedSignal()`은 `Dali::Ui::UiThemeManager`가 소유한 테마 변경 시그널을 반환합니다. 시그널 타입은 `Dali::Signal<void()>`이므로 여기에 연결되는 콜백은 인수를 받지 않습니다.

시그널 연결을 소유하는 수신 객체에는 `Dali::ConnectionTracker`를 사용합니다.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/ui-theme-manager.h>

class ThemeObserver : public Dali::ConnectionTracker
{
public:
  ThemeObserver()
  {
    Dali::Ui::UiThemeManager themeManager = Dali::Ui::UiThemeManager::Get();
    themeManager.ThemeChangedSignal().Connect(this, &ThemeObserver::OnThemeChanged);
  }

private:
  void OnThemeChanged()
  {
    auto themeId = Dali::Ui::UiThemeManager::Get().GetCurrentThemeId();
    ApplyThemeId(themeId);
  }

  void ApplyThemeId(const decltype(Dali::Ui::UiThemeManager::Get().GetCurrentThemeId())& themeId)
  {
    mLastThemeId = themeId;
  }

  decltype(Dali::Ui::UiThemeManager::Get().GetCurrentThemeId()) mLastThemeId;
};
```

테마 변경 콜백은 작게 유지하는 것이 좋습니다. `Dali::Ui::UiThemeManager::GetCurrentThemeId()`로 현재 식별자를 읽은 뒤, dali-ui 뷰를 구동하는 애플리케이션 상태를 갱신합니다.

## 뷰 중심 애플리케이션 계층에서 테마 상태 사용 {#using-theme-state-from-a-view-oriented-app-layer}

dali-ui 애플리케이션에서는 테마 변경에 대한 처리를 `Dali::Ui::View` 트리를 소유한 코드 가까이에 두는 것이 좋습니다. 테마 식별과 테마 변경 알림을 다룰 때 `Dali::Ui::UiThemeManager`는 애플리케이션이 사용하는 진입점입니다. 이를 사용하면 애플리케이션 코드에서 원시 액터를 직접 조작하거나 저수준 속성을 설정할 필요가 없습니다.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/ui-theme-manager.h>

class MainViewThemeBinding : public Dali::ConnectionTracker
{
public:
  MainViewThemeBinding()
  : mThemeManager(Dali::Ui::UiThemeManager::Get())
  {
    mThemeManager.ThemeChangedSignal().Connect(this, &MainViewThemeBinding::OnThemeChanged);
    RefreshFromTheme();
  }

private:
  void OnThemeChanged()
  {
    RefreshFromTheme();
  }

  void RefreshFromTheme()
  {
    mCurrentThemeId = mThemeManager.GetCurrentThemeId();

    // Use mCurrentThemeId to update state that your Dali::Ui::View layer consumes.
  }

  Dali::Ui::UiThemeManager mThemeManager;
  decltype(Dali::Ui::UiThemeManager::Get().GetCurrentThemeId()) mCurrentThemeId;
};
```

`Dali::Ui::UiThemeManager`는 속성 상수를 노출하지 않습니다. 애플리케이션 코드에서는 `Dali::Ui::UiThemeManager::Get()`, `Dali::Ui::UiThemeManager::GetCurrentThemeId()`, `Dali::Ui::UiThemeManager::ThemeChangedSignal()`을 공개 테마 관리자 인터페이스로 사용합니다.
