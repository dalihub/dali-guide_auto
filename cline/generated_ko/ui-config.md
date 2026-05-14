---
title: Ui 설정
sidebar_label: Ui 설정
category: styling-theme-config
---

# Ui 설정

`UiConfig`는 dali-ui 전역 설정을 위한 초기화 후 불변인 중앙 집중식 구성 객체를 제공합니다. 단위 계산(spx, dp, sdp) 및 전체 dali-ui 프레임워크의 기본 동작에 영향을 미치는 디스플레이 및 스케일링 매개변수를 보유합니다.

## 목차

- [기본 설정 및 적용](#기본-설정-및-적용)
- [디스플레이 스케일링 및 DPI 구성](#디스플레이-스케일링-및-dpi-구성)
- [텍스트 및 레이블 기본값](#텍스트-및-레이블-기본값)

## 기본 설정 및 적용

`UiConfig`는 정확히 한 번 생성되고 적용되어야 하며, 일반적으로 애플리케이션 메인 루프가 시작되기 전 `main()`에서 수행됩니다. `Apply()`가 호출된 후 구성은 고정되고 setter 메서드를 더 이상 사용할 수 없습니다.

`UiConfig::New()`로 새 `UiConfig`를 생성하고, 플루언트 메서드 체이닝을 사용하여 구성하고, `Apply()`로 적용합니다:

```cpp
int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);

  UiConfig::New()
    .SetScalingFactor(1.5f)
    .SetDpi(320)
    .Apply();

  MyController controller(application);
  application.MainLoop();

  return 0;
}
```

가장 간단한 구성은 사용자 정의 없이 기본값을 적용합니다:

```cpp
UiConfig::New().Apply();
```

모든 setter 메서드는 `UiConfig` 객체에 대한 참조를 반환하여 플루언트 체이닝을 가능하게 합니다. 기본값은 `scalingFactor=1.0f`, `dpi=160`, `baselineDpi=160`입니다.

## 디스플레이 스케일링 및 DPI 구성

`UiConfig`는 다양한 화면 밀도에서 반응형 레이아웃을 위해 dali-ui가 장치 독립 단위(dp, sdp, spx)를 계산하는 방법을 제어합니다.

### 스케일링 팩터

스케일링 팩터는 spx 및 sdp 단위 계산에 영향을 미칩니다. `SetScalingFactor()`로 구성합니다:

```cpp
UiConfig::New()
  .SetScalingFactor(1.5f)
  .Apply();
```

`GetScalingFactor()`로 현재 스케일링 팩터를 조회합니다:

```cpp
float scale = config.GetScalingFactor();
```

### DPI 구성

dp 및 sdp 단위 계산을 위해 `SetDpi()`로 화면 DPI를 설정합니다:

```cpp
UiConfig::New()
  .SetDpi(320)
  .Apply();
```

`GetDpi()`로 현재 DPI를 조회합니다:

```cpp
int dpi = config.GetDpi();
```

### 기준 DPI

기준 DPI는 dp 단위 계산의 기준선을 정의합니다:

```cpp
UiConfig::New()
  .SetBaselineDpi(160)
  .Apply();
```

## 텍스트 및 레이블 기본값

### 기본 폰트 크기

```cpp
UiConfig::New()
  .SetDefaultFontSize(16.0f)
  .Apply();
```

### 텍스트 렌더링 품질

```cpp
UiConfig::New()
  .SetTextQualityHigh(true)
  .Apply();
```
