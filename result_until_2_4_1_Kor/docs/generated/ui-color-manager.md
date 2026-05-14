---
title: UI 색상 관리자
sidebar_label: UI 색상 관리자
category: styling-theme-config
---

# UI 색상 관리자 {#ui-color-manager}

`Dali::Ui::UiColorManager`는 dali-ui 앱에서 테마 색상을 해석하고, 식별자 기반 `Dali::Ui::UiColor` 값을 `Dali::Ui::View` 객체에 연결해 유지하며, 임시 색상 오버라이드를 적용할 수 있는 단일 지점을 제공합니다.

## 목차 {#table-of-contents}

- [색상 관리자 가져오기](#getting-the-color-manager)
- [테마 색상 조회하기](#looking-up-theme-colors)
- [뷰에 색상 바인딩하기](#binding-colors-to-views)
- [바인딩 교체 또는 해제하기](#replacing-or-clearing-bindings)
- [테마 색상 오버라이드하기](#overriding-theme-colors)
- [캐시된 색상 새로 고치기](#refreshing-cached-colors)

## 색상 관리자 가져오기 {#getting-the-color-manager}

`Dali::Ui::UiColorManager`는 핸들 기반 싱글턴입니다. 애플리케이션 코드에서 현재 관리자가 필요할 때는 `Dali::Ui::UiColorManager::Get`을 사용합니다. 기본 생성된 `Dali::Ui::UiColorManager`는 초기화되지 않은 핸들이며, 값을 할당하기 전 핸들 변수로만 유용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void PrepareThemeAccess()
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  if(manager)
  {
    Dali::Vector4 primary = manager.GetColor("Primary");
  }
}
```

관리자 핸들이 일반 `Dali::BaseHandle`을 통해 전달된 경우에는 `Dali::Ui::UiColorManager::DownCast`로 복구합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::UiColorManager ResolveManager(Dali::BaseHandle handle)
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::DownCast(handle);

  if(!manager)
  {
    manager = Dali::Ui::UiColorManager::Get();
  }

  return manager;
}
```

## 테마 색상 조회하기 {#looking-up-theme-colors}

활성 테마에서 색상 식별자를 해석하려면 `Dali::Ui::UiColorManager::GetColor`를 사용합니다. 단일 반환값 오버로드는 `Dali::Vector4` RGBA 값을 반환하며, 색상 식별자를 해석할 수 없으면 `Dali::Vector4::ZERO`를 반환합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Vector4 ResolvePanelColor()
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  Dali::Vector4 background = manager.GetColor("Background");
  if(background == Dali::Vector4::ZERO)
  {
    return Dali::Vector4(0.08f, 0.08f, 0.08f, 1.0f);
  }

  return background;
}
```

테마 조회가 필요 없는 직접 RGBA 값은 컴포넌트 값을 사용해 `Dali::Ui::UiColor`를 생성합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::UiColor MakeDimOverlayColor()
{
  return Dali::Ui::UiColor(0.0f, 0.0f, 0.0f, 0.45f);
}
```

## 뷰에 색상 바인딩하기 {#binding-colors-to-views}

바인딩은 객체의 이름 있는 색상 슬롯을 콜백에 연결합니다. 앱에서 사용하는 코드에서는 대상 객체가 일반적으로 `Dali::Ui::View` 핸들이어야 합니다. `Dali::Ui::UiColorManager::RegisterBinding`으로 바인딩을 등록한 다음, `Dali::Ui::UiColorManager::SetBindingColor`로 해당 바인딩에 사용할 식별자 기반 `Dali::Ui::UiColor`를 저장합니다.

`Dali::Ui::UiColorManager::RegisterBinding`은 콜백을 즉시 호출하지 않습니다. 관리자는 테마가 변경된 뒤나 테마 오버라이드가 설정 또는 해제된 뒤처럼 바인딩이 새로 고쳐질 때 등록된 콜백을 호출합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
Dali::Vector4 gLastNavigationColor;

void ApplyNavigationColor(const Dali::Vector4& color)
{
  gLastNavigationColor = color;
}
}

void BindNavigationColor(Dali::Ui::View navigationView)
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.RegisterBinding(
    navigationView,
    "NavigationColor",
    Dali::Ui::ColorCallback::New(ApplyNavigationColor));

  manager.SetBindingColor(
    navigationView,
    "NavigationColor",
    Dali::Ui::UiColor("Primary"));
}
```

설정 코드가 두 번 이상 실행될 수 있다면 `Dali::Ui::UiColorManager::HasBinding`을 사용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
void ApplyContentColor(const Dali::Vector4& color)
{
}
}

void EnsureContentColorBinding(Dali::Ui::View contentView)
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  if(!manager.HasBinding(contentView, "ContentColor"))
  {
    manager.RegisterBinding(
      contentView,
      "ContentColor",
      Dali::Ui::ColorCallback::New(ApplyContentColor));
  }

  manager.SetBindingColor(
    contentView,
    "ContentColor",
    Dali::Ui::UiColor("Background"));
}
```

## 바인딩 교체 또는 해제하기 {#replacing-or-clearing-bindings}

같은 `Dali::Ui::View`와 바인딩 식별자로 `Dali::Ui::UiColorManager::SetBindingColor`를 다시 호출하면 해당 바인딩에 저장된 `Dali::Ui::UiColor`가 교체됩니다. 해당 식별자의 바인딩이 없으면 `Dali::Ui::UiColorManager::SetBindingColor`는 아무 효과가 없습니다.

현재 저장된 색상을 확인하려면 `Dali::Ui::UiColorManager::GetBindingColor`를 사용하고, 하나의 바인딩을 제거하려면 `Dali::Ui::UiColorManager::ClearBinding`을 사용하며, 한 뷰의 모든 바인딩을 제거하려면 `Dali::Ui::UiColorManager::ClearBindings`를 사용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
void ApplyAccentColor(const Dali::Vector4& color)
{
}
}

void ReplaceAccentBinding(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.RegisterBinding(
    view,
    "AccentColor",
    Dali::Ui::ColorCallback::New(ApplyAccentColor));

  manager.SetBindingColor(view, "AccentColor", Dali::Ui::UiColor("Primary"));
  manager.SetBindingColor(view, "AccentColor", Dali::Ui::UiColor("Outline"));

  Dali::Ui::UiColor storedColor;
  if(manager.GetBindingColor(view, "AccentColor", storedColor))
  {
    Dali::Vector4 resolvedColor = storedColor.GetRgba();
    ApplyAccentColor(resolvedColor);
  }
}

void ClearAccentBinding(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.ClearBinding(view, "AccentColor");
}

void ClearAllThemeBindings(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.ClearBindings(view);
}
```

## 테마 색상 오버라이드하기 {#overriding-theme-colors}

`Dali::Ui::UiColorManager::SetColorOverride`는 일반 테마 조회보다 먼저 호출되는 함수를 설치합니다. 이 함수는 색상 식별자와 출력용 `Dali::Vector4`를 받습니다. 테마 값을 대체하려면 출력 색상을 쓴 뒤 `true`를 반환하고, 일반 테마 조회를 계속 진행하려면 `false`를 반환합니다.

오버라이드는 일반 함수 포인터인 `Dali::Ui::ColorOverrideFunc`와 일치해야 합니다. 자유 함수나 상태 없는 람다를 사용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
bool HighContrastOverride(Dali::StringView colorId, Dali::Vector4& outColor)
{
  if(colorId == "Primary")
  {
    outColor = Dali::Vector4(1.0f, 1.0f, 1.0f, 1.0f);
    return true;
  }

  if(colorId == "Background")
  {
    outColor = Dali::Vector4(0.0f, 0.0f, 0.0f, 1.0f);
    return true;
  }

  return false;
}
}

void EnableHighContrastTheme()
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.SetColorOverride(HighContrastOverride);
}

void DisableHighContrastTheme()
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.ClearColorOverride();
}
```

새 오버라이드를 설정하면 이전 오버라이드가 교체되고 기존 뷰 바인딩이 새로 고쳐집니다. `Dali::Ui::UiColorManager::SetColorOverride`에 `nullptr`을 전달하면 오버라이드가 해제되며, 실제 효과는 `Dali::Ui::UiColorManager::ClearColorOverride`를 호출하는 것과 같습니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ResetThemeOverride()
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.SetColorOverride(nullptr);
}
```

## 캐시된 색상 새로 고치기 {#refreshing-cached-colors}

`Dali::Ui::UiColorManager::InvalidateCache`는 해석되어 캐시된 색상 값을 지웁니다. 설치된 오버라이드가 내부에서 사용하는 데이터를 변경하는 경우 이 함수를 호출하면 이후 색상 해석에서 현재 오버라이드 결과를 읽습니다. 이 작업은 캐시된 값만 무효화하며, 그 자체로 바인딩 콜백을 호출하지는 않습니다.

무효화할 특정 색상 값이 코드에 있을 때는 `Dali::Ui::UiColor`를 받는 오버로드를 사용합니다. 직접 RGBA 색상은 테마 해석이 필요 없으므로, 이 오버로드는 주로 식별자 기반 `Dali::Ui::UiColor` 값에 유용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
bool gUseWarningPalette = false;

bool WarningOverride(Dali::StringView colorId, Dali::Vector4& outColor)
{
  if(gUseWarningPalette && colorId == "Primary")
  {
    outColor = Dali::Vector4(1.0f, 0.32f, 0.0f, 1.0f);
    return true;
  }

  return false;
}
}

void InstallWarningPaletteOverride()
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.SetColorOverride(WarningOverride);
}

void SetWarningPaletteEnabled(bool enabled)
{
  gUseWarningPalette = enabled;

  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();
  manager.InvalidateCache();
}

void RefreshOneStoredColor()
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();
  Dali::Ui::UiColor color("Primary");

  manager.InvalidateCache(color);
}
```
