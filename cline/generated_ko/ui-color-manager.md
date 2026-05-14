---
title: Ui 색상 관리자
sidebar_label: Ui 색상 관리자
category: styling-theme-config
---

# Ui 색상 관리자

`UiColorManager`는 애플리케이션의 색상 테이블에 대한 중앙 집중식 접근을 제공하여 테마 기반 색상 조회 및 뷰에 대한 동적 색상 바인딩을 가능하게 합니다.

## 목차

- [색상 관리자 접근](#색상-관리자-접근)
- [테마 색상 조회](#테마-색상-조회)
- [뷰에 색상 바인딩](#뷰에-색상-바인딩)
- [테마 색상 재정의](#테마-색상-재정의)

## 색상 관리자 접근

`UiColorManager`는 싱글톤입니다. `UiColorManager::Get()`으로 인스턴스를 얻습니다:

```cpp
#include <dali-ui-foundation/public-api/ui-color-manager.h>

using namespace Dali::Ui;

UiColorManager manager = UiColorManager::Get();
```

반환된 핸들은 애플리케이션 수명 동안 유효합니다. 복사 및 이동 의미 체계가 지원됩니다:

```cpp
UiColorManager copy = UiColorManager::Get();  // 복사 생성자
UiColorManager moved(std::move(copy));         // 이동 생성자
```

`BaseHandle`을 `UiColorManager`로 다운캐스트하려면:

```cpp
BaseHandle handle = UiColorManager::Get();
UiColorManager manager = UiColorManager::DownCast(handle);
```

## 테마 색상 조회

### ID로 색상 검색

`GetColor()`로 문자열 식별자로 색상을 조회합니다. 이 메서드는 해결된 RGBA 값을 반환하거나, 색상을 찾을 수 없으면 `Vector4::ZERO`를 반환합니다:

```cpp
UiColorManager manager = UiColorManager::Get();

Vector4 primary = manager.GetColor("Primary");
```

성공 여부를 나타내는 불리언 반환을 제공하는 오버로드가 있습니다:

```cpp
Vector4 outColor;
bool found = manager.GetColor("Primary", outColor);

if (found) {
  // outColor 사용
}
```

### 기본 테마 색상

기본 테마는 표준 색상 식별자를 제공합니다:

- `"Primary"` — 주요 강조 색상
- `"Background"` — 배경색
- `"Outline"` — 외곽선/테두리 색상

```cpp
Vector4 primary, background, outline;

manager.GetColor("Primary", primary);
manager.GetColor("Background", background);
manager.GetColor("Outline", outline);
```

## 뷰에 색상 바인딩

색상을 뷰 속성에 바인딩하여 테마가 변경될 때 자동으로 업데이트됩니다:

```cpp
UiColorManager manager = UiColorManager::Get();

// 배경색을 테마 색상에 바인딩
manager.BindColor(view, View::Property::BACKGROUND_COLOR, "Primary");
```

## 테마 색상 재정의

런타임에 테마 색상을 재정의합니다:

```cpp
UiColorManager manager = UiColorManager::Get();

// 색상 재정의
manager.SetColor("Primary", Vector4(1.0f, 0.0f, 0.0f, 1.0f)); // 빨간색으로 재정의

// 재정의 제거
manager.ResetColor("Primary");
```
