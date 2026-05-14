---
title: Ui 색상
sidebar_label: Ui 색상
category: styling-theme-config
---

# Ui 색상

UiColor는 dali-ui 애플리케이션을 위한 테마 인식 색상 추상화를 제공합니다. 명시적 RGBA 값과 런타임에 해결되는 테마 색상 참조를 모두 지원합니다.

## 목차

- [UiColor 인스턴스 생성](#uicolor-인스턴스-생성)
- [테마 색상 참조](#테마-색상-참조)
- [알파 조작](#알파-조작)
- [미리 정의된 테마 색상](#미리-정의된-테마-색상)
- [Vector4로 변환](#vector4로-변환)

## UiColor 인스턴스 생성

UiColor는 다양한 사용 사례를 위한 여러 생성자를 제공합니다.

### RGBA 값

명시적 빨강, 초록, 파랑, 알파 구성 요소로 UiColor를 생성합니다. 값은 0.0~1.0 범위입니다.

```cpp
// 전체 RGBA
UiColor red(1.0f, 0.0f, 0.0f, 1.0f);

// 기본 알파 1.0인 RGB
UiColor gray(0.5f, 0.5f, 0.5f);
```

### 16진수 RGB 값

16진수 RGB 값에서 UiColor를 생성합니다. 알파는 기본적으로 1.0입니다.

```cpp
// 0xFF0000 = 순수 빨강
UiColor red(0xFF0000u);

// 명시적 알파가 있는 16진수
UiColor translucentGreen(0x00FF00u, 0.5f);
```

### Vector4 값

기존 Vector4에서 UiColor를 생성합니다.

```cpp
Vector4 input(0.2f, 0.4f, 0.6f, 0.8f);
UiColor color(input);
```

### 기본 생성

기본 생성된 UiColor는 색상 ID가 없으며 투명한 검정(Vector4::ZERO)으로 해결됩니다.

```cpp
UiColor color;
// color.HasColorId() == false
// color.GetRgba() == Vector4::ZERO
```

## 테마 색상 참조

UiColor는 문자열 식별자로 테마 색상을 참조할 수 있습니다. 실제 RGBA 값은 런타임에 테마 시스템을 통해 해결됩니다.

### 테마 색상 참조 생성

```cpp
// ID로 테마 색상 참조
UiColor themeColor("ThemeColor1");

// 색상이 테마 참조인지 확인
if (themeColor.HasColorId()) {
    String id = themeColor.GetColorId(); // "ThemeColor1"
}
```

### 테마 색상 해결

`GetRgba()`를 호출하여 색상을 RGBA 값으로 해결합니다. 알 수 없는 테마 ID는 Vector4::ZERO를 반환합니다.

```cpp
UiColor color("Primary");
Vector4 resolved = color.GetRgba();

// 알 수 없는 테마 색상은 투명으로 폴백
UiColor unknown("NonExistentColorId");
Vector4 fallback = unknown.GetRgba(); // Vector4::ZERO
```

### 뷰와 함께 사용

테마 인식 스타일링을 위해 뷰 배경에 UiColor를 적용합니다.

```cpp
// 미리 정의된 테마 색상 사용
view.SetBackgroundColor(UiColor::PRIMARY);

// 커스텀 테마 색상 사용
view.SetBackgroundColor(UiColor("AccentColor"));
```

## 알파 조작

`WithAlpha()` 메서드는 기존 색상의 알파 값을 수정한 새 UiColor를 반환합니다.

```cpp
UiColor semiRed = UiColor(0xFF0000).WithAlpha(0.5f);
```

## 미리 정의된 테마 색상

UiColor는 일반적인 테마 색상에 대한 정적 상수를 제공합니다:

```cpp
UiColor::PRIMARY          // 주요 브랜드 색상
UiColor::SECONDARY        // 보조 브랜드 색상
UiColor::EMPHASIS         // 강조 색상
UiColor::SURFACE          // 표면 배경
UiColor::BACKGROUND       // 일반 배경
UiColor::TEXT             // 기본 텍스트 색상
UiColor::TEXT_SECONDARY   // 보조 텍스트 색상
```

## Vector4로 변환

`GetRgba()` 메서드는 UiColor를 Vector4 RGBA 값으로 변환합니다.

```cpp
UiColor color(0xFF8040);
Vector4 rgba = color.GetRgba(); // (1.0, 0.5, 0.25, 1.0)
```
