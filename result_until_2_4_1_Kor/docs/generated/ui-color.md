---
title: UI 색상
sidebar_label: UI 색상
category: styling-theme-config
---

# UI 색상 {#ui-color}

`Dali::Ui::UiColor`는 dali-ui 색상을 직접 RGBA 값 또는 테마에서 해석되는 색상 ID로 표현합니다.

## 목차 {#table-of-contents}

- [dali-ui 뷰에서 `UiColor` 사용](#use-uicolor-with-dali-ui-views)
- [테마 색상 또는 직접 RGBA 색상 선택](#choose-theme-colors-or-direct-rgba-colors)
- [색상 검사 및 해석](#inspect-and-resolve-colors)
- [원래 색상 의도를 유지하면서 알파 조정](#adjust-alpha-without-losing-the-original-color-intent)

## dali-ui 뷰에서 `UiColor` 사용 {#use-uicolor-with-dali-ui-views}

dali-ui 애플리케이션에서는 원시 액터 속성을 직접 설정하는 대신 `Dali::Ui::View`의 색상 API와 함께 `Dali::Ui::UiColor`를 사용합니다. 뷰는 `Dali::Ui::UiColor::BACKGROUND` 또는 `Dali::Ui::UiColor::PRIMARY`와 같은 미리 정의된 테마 색상을 받을 수 있습니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyPanelColors(Dali::Ui::View& panel, Dali::Ui::View& action)
{
  panel.SetBackgroundColor(Dali::Ui::UiColor::BACKGROUND);
  action.SetBackgroundColor(Dali::Ui::UiColor::PRIMARY);
}
```

`Dali::Ui::View::SetBackgroundColor`는 `const Dali::Ui::UiColor&`를 받고 뷰를 반환하므로 일반적인 dali-ui 플루언트 체이닝에 자연스럽게 사용할 수 있습니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::View MakePrimaryTile()
{
  return Dali::Ui::View::New()
    .SetBackgroundColor(Dali::Ui::UiColor::PRIMARY);
}
```

`Dali::Ui::UiColor`에 색상 ID가 들어 있으면 `Dali::Ui::View::SetBackgroundColor`는 현재 테마에서 해당 ID를 해석하고 바인딩을 등록합니다. 따라서 테마가 변경될 때 배경이 갱신됩니다. 직접 RGBA `Dali::Ui::UiColor`가 적용되면 직접 값이 즉시 사용되며, 해당 뷰 배경에 대해 이전에 설정된 ID 기반 바인딩은 제거됩니다.

## 테마 색상 또는 직접 RGBA 색상 선택 {#choose-theme-colors-or-direct-rgba-colors}

색상이 활성 테마를 따라야 하는 경우 미리 정의된 ID 색상을 사용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyThemeRoleColors(Dali::Ui::View& root, Dali::Ui::View& primarySurface, Dali::Ui::View& outlineSurface)
{
  root.SetBackgroundColor(Dali::Ui::UiColor::BACKGROUND);
  primarySurface.SetBackgroundColor(Dali::Ui::UiColor::PRIMARY);
  outlineSurface.SetBackgroundColor(Dali::Ui::UiColor::OUTLINE);
}
```

색상이 테마에서 해석되지 않고 애플리케이션에서 고정되어야 하는 경우 RGBA 생성자 `Dali::Ui::UiColor(float r, float g, float b, float a = 1.0f)`를 사용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyFixedStateColors(Dali::Ui::View& success, Dali::Ui::View& warning)
{
  const Dali::Ui::UiColor successGreen(0.0f, 0.70f, 0.25f, 1.0f);
  const Dali::Ui::UiColor warningAmber(1.0f, 0.65f, 0.0f);

  success.SetBackgroundColor(successGreen);
  warning.SetBackgroundColor(warningAmber);
}
```

네 개의 RGBA 구성 요소는 부동소수점 값입니다. 알파 인수의 기본값은 `1.0f`이므로 `Dali::Ui::UiColor(1.0f, 0.0f, 0.0f)`는 불투명한 빨간색을 생성합니다. `Dali::Ui::UiColor`는 `Dali::Vector4`, 문자열 색상 ID, `0xRRGGBB` 16진수 값을 받는 생성자도 제공합니다.

## 색상 검사 및 해석 {#inspect-and-resolve-colors}

`Dali::Ui::UiColor::HasColorId`는 색상이 ID 기반인지 여부를 알려 줍니다. `Dali::Ui::UiColor::GetColorId`는 ID 기반 색상에 대해 ID 문자열을 반환하고, 직접 RGBA 색상에 대해서는 빈 문자열을 반환합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

bool IsPrimaryRole(const Dali::Ui::UiColor& color)
{
  return color.HasColorId() && color.GetColorId() == "Primary";
}
```

`Dali::Ui::UiColor::GetRgba`는 색상을 `Dali::Vector4`로 해석합니다. 직접 RGBA 색상은 저장된 구성 요소를 반환합니다. ID 기반 색상은 해석되는 시점에 현재 테마에서 조회되며, ID를 찾을 수 없으면 대체 값이 반환됩니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

float ResolvedAlpha(const Dali::Ui::UiColor& color)
{
  const Dali::Vector4 rgba = color.GetRgba();
  return rgba.a;
}
```

`Dali::Ui::UiColor`는 `Dali::Ui::UiColor::GetRgba`로 해석하는 것과 동일한 `Dali::Ui::UiColor::operator Vector4`도 제공합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Vector4 ResolveForComparison(const Dali::Ui::UiColor& color)
{
  Dali::Vector4 rgba = color;
  return rgba;
}
```

일반적인 뷰 스타일링에서는 `Dali::Ui::UiColor`를 dali-ui 뷰 API에 전달하여 뷰가 테마를 인식하는 값을 유지하도록 합니다. 비교나 사용자 지정 계산처럼 해석된 숫자 색상이 특별히 필요할 때 `Dali::Ui::UiColor::GetRgba`를 사용합니다.

## 원래 색상 의도를 유지하면서 알파 조정 {#adjust-alpha-without-losing-the-original-color-intent}

알파 값을 바꾸고 새 `Dali::Ui::UiColor`를 반환하려면 `Dali::Ui::UiColor::WithAlpha`를 사용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyDimmedPrimary(Dali::Ui::View& view)
{
  const Dali::Ui::UiColor dimmedPrimary = Dali::Ui::UiColor::PRIMARY.WithAlpha(0.40f);
  view.SetBackgroundColor(dimmedPrimary);
}
```

현재 알파에 배율을 곱하고 새 `Dali::Ui::UiColor`를 반환하려면 `Dali::Ui::UiColor::ScaleAlpha`를 사용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyPressedState(Dali::Ui::View& view)
{
  const Dali::Ui::UiColor pressed = Dali::Ui::UiColor::PRIMARY.ScaleAlpha(0.75f);
  view.SetBackgroundColor(pressed);
}
```

두 알파 헬퍼는 모두 직접 RGBA 색상과 ID 기반 색상의 구분을 보존합니다. 예를 들어 `Dali::Ui::UiColor::PRIMARY`에서 파생된 색상은 알파를 조정한 후에도 여전히 `"Primary"` 색상 ID를 가집니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

bool KeepsThemeRoleAfterAlphaChange()
{
  const Dali::Ui::UiColor color = Dali::Ui::UiColor::PRIMARY.WithAlpha(0.5f).ScaleAlpha(0.5f);

  return color.HasColorId() && color.GetColorId() == "Primary";
}
```

`Dali::Ui::UiColor::WithAlpha` 또는 `Dali::Ui::UiColor::ScaleAlpha`를 사용할 때 알파 값은 유효 범위로 클램프됩니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::UiColor MakeVisibleOverlay()
{
  return Dali::Ui::UiColor(0.0f, 0.0f, 0.0f, 0.80f)
    .ScaleAlpha(0.5f);
}
```
