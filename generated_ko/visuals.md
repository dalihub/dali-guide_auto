---
title: 비주얼
sidebar_label: 비주얼
category: images-visuals
---

# 비주얼

비주얼은 `View`에 첨부하여 이미지, 색상 또는 애니메이션을 표시할 수 있는 렌더링 가능한 객체입니다. 뷰 계층 내에서 시각적 요소를 구성하는 선언적 방법을 제공합니다.

## 목차

- [비주얼 타입](#비주얼-타입)
- [뷰에 비주얼 첨부](#뷰에-비주얼-첨부)
- [변형 및 위치 지정](#변형-및-위치-지정)
- [모서리 반경 및 테두리](#모서리-반경-및-테두리)
- [ImageVisual](#imagevisual)
- [AnimatedImageVisual](#animatedimagevisual)
- [ColorVisual](#colorvisual)

## 비주얼 타입

dali-ui 프레임워크는 여러 비주얼 타입을 제공합니다:

- **ColorVisual** - 선택적 블러 및 컷아웃 효과가 있는 단색 렌더링
- **ImageVisual** - N-패치 및 마스킹 지원이 있는 정적 이미지 표시
- **AnimatedImageVisual** - 애니메이션 이미지 시퀀스 재생 (GIF, WebP 또는 이미지 목록)

모든 비주얼 타입은 `VisualBase`를 상속하며, 위치 지정, 크기 조정, 장식을 위한 공통 속성을 제공합니다.

## 뷰에 비주얼 첨부

비주얼은 `Visuals()` 메서드나 `AddVisual()`을 사용하여 `View`에 첨부합니다. 각 비주얼은 뷰 콘텐츠에 대한 렌더링 순서를 결정하는 특정 컨테이너 범위에 배치됩니다.

```cpp
View view = View::New()
  .SetRequestedWidth(200.0f)
  .SetRequestedHeight(200.0f)
  .Visuals(
    Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT,
    {
      ColorVisual::New()
        .SetColor(UiColor(0x3F0F0F).WithAlpha(0.2f))
        .SetWidth(1.0f)
        .SetHeight(1.0f)
        .SetProportionFlags(Visual::Transform::ProportionFlags::SIZE_PROPORTIONAL),
    });
```

`ContainerRangeType` 열거형은 비주얼이 렌더링되는 위치를 정의합니다:

- `UNDER_BACKGROUND_EFFECT` - 뷰의 배경 효과 아래
- `BETWEEN_BACKGROUND_EFFECT_AND_BACKGROUND` - 배경 효과와 배경 사이
- `BETWEEN_BACKGROUND_AND_CONTENT` - 배경과 콘텐츠 사이
- `BETWEEN_CONTENT_AND_DECORATION` - 콘텐츠와 장식 사이
- `BETWEEN_DECORATION_AND_FOREGROUND_EFFECT` - 장식과 전경 효과 사이
- `OVER_FOREGROUND_EFFECT` - 뷰의 전경 효과 위

`AddVisual()`로 동적으로 비주얼을 추가할 수도 있습니다:

```cpp
view.AddVisual(
  ImageVisual::New()
    .SetResourceUrl("image.png")
    .SetWidth(0.5f)
    .SetHeight(0.5f),
  Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT
);
```

비주얼을 제거하려면 `RemoveVisual()`을 사용합니다:

```cpp
uint32_t count = view.GetVisualCount(Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT);
if (count > 0)
{
  auto visual = view.GetVisualAt(Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT, count - 1);
  view.RemoveVisual(visual);
}
```

## 변형 및 위치 지정

`VisualBase`는 부모 뷰 내에서 비주얼의 위치와 크기를 제어하는 속성을 제공합니다.

### 위치

`SetOffsetX()`와 `SetOffsetY()`로 비주얼을 위치 지정합니다. 기본적으로 값은 부모 뷰 크기의 비율입니다.

```cpp
ColorVisual::New()
  .SetOffsetX(0.25f)  // 원점에서 25%
  .SetOffsetY(0.25f)  // 원점에서 25%
```

### 크기

`SetWidth()`와 `SetHeight()`로 비주얼 크기를 설정합니다. 기본적으로 부모 뷰에 비례합니다.

```cpp
ImageVisual::New()
  .SetResourceUrl("image.png")
  .SetWidth(0.5f)   // 부모 너비의 50%
  .SetHeight(0.5f)  // 부모 높이의 50%
```

### 비율 플래그

`SetProportionFlags()` 메서드는 위치와 크기 값이 비율로 해석되는지 절대값으로 해석되는지 제어합니다:

```cpp
ImageVisual::New()
  .SetResourceUrl("image.png")
  .SetOffsetX(10.0f)
  .SetOffsetY(20.0f)
  .SetWidth(100.0f)
  .SetHeight(100.0f)
  .SetProportionFlags(Visual::Transform::ProportionFlags::ALL)  // 모든 값이 비율
```

`Visual::Transform::ProportionFlags::SIZE_PROPORTIONAL`을 사용하여 오프셋은 절대값으로 유지하면서 크기 값만 비율로 만듭니다.

### 원점 및 피벗

`SetOrigin()`과 `SetPivot()` 메서드는 위치 지정을 위한 기준점을 정의합니다:

```cpp
ColorVisual::New()
  .SetOrigin(Align::TOP_CENTER)   // 부모의 상단 중앙 기준 위치
  .SetPivot(Align::TOP_CENTER)    // 비주얼의 상단 중앙이 원점에 정렬
```

## 모서리 반경 및 테두리

모든 비주얼 타입은 모서리 반경과 테두리 장식을 지원합니다.

### 모서리 반경

`SetCornerRadius()`로 모서리 반경을 설정합니다:

```cpp
ImageVisual::New()
  .SetResourceUrl("image.png")
  .SetCornerRadius(0.25f)  // 25% 모서리 반경
  .SetCornerRadiusPolicyRelative()
```

개별 모서리 제어:

```cpp
ColorVisual::New()
  .SetCornerRadius(0.25f, 0.0f, 0.0f, 0.25f)  // 왼쪽상단, 오른쪽상단, 오른쪽하단, 왼쪽하단
  .SetCornerRadiusPolicyRelative()
```

`SetCornerRadiusPolicy()`로 픽셀 값은 `CornerRadiusPolicy::ABSOLUTE`, 비율 값은 `CornerRadiusPolicy::RELATIVE`을 사용합니다.

### 테두리

`SetBorderlineWidth()`, `SetBorderlineColor()`, `SetBorderlineOffset()`으로 테두리 외곽선을 추가합니다:

```cpp
ColorVisual::New()
  .SetColor(UiColor(0x003070))
  .SetCornerRadius(0.25f)
  .SetCornerRadiusPolicyRelative()
  .SetBorderlineWidth(2_spx)
  .SetBorderlineColor(UiColor(0x3F3F3F).WithAlpha(0.5f))
  .SetBorderlineOffset(-0.98f)
```

## ImageVisual

`ImageVisual`은 다양한 로딩 및 렌더링 옵션을 지원하는 정적 이미지를 표시합니다.

### 기본 사용법

```cpp
ImageVisual::New()
  .SetResourceUrl("image.png")
  .SetWidth(1.0f)
  .SetHeight(1.0f)
```

### 로딩 옵션

`SetSynchronousLoading()`과 `SetLoadPolicy()`로 이미지 로딩 동작을 제어합니다:

```cpp
ImageVisual::New()
  .SetResourceUrl("image.png")
  .SetSynchronousLoading(true)  // 메인 스레드에서 로드
  .SetLoadPolicy(Image::LoadPolicy::ATTACHED)  // 뷰에 첨부될 때 로드
```

### 크기 옵션

원하는 크기 또는 피팅 모드를 설정합니다:

```cpp
ImageVisual::New()
  .SetResourceUrl("image.png")
  .SetDesiredWidth(200)
  .SetDesiredHeight(150)
  .SetFittingMode(Image::FittingMode::FIT_KEEP_ASPECT_RATIO)
```

### 알파 마스킹

이미지에 알파 마스크를 적용합니다:

```cpp
ImageVisual::New()
  .SetResourceUrl("image.png")
  .SetAlphaMaskUrl("mask.png")
  .SetMaskContentScale(1.0f)
  .SetCropToMask(true)
```

## AnimatedImageVisual

`AnimatedImageVisual`은 GIF, WebP 또는 이미지 URL 목록에서 애니메이션 이미지를 재생합니다.

### 기본 사용법

애니메이션 GIF 로드:

```cpp
AnimatedImageVisual::New()
  .SetResourceUrl("animation.gif")
  .SetWidth(1.0f)
  .SetHeight(1.0f)
  .Play()
```

### 이미지 시퀀스

프레임별 애니메이션을 위해 이미지 URL 목록을 사용합니다:

```cpp
AnimatedImageVisual::New()
  .SetResourceUrlList({
    "frame01.png",
    "frame02.png",
    "frame03.png",
  })
  .SetFrameDelay(100)  // 프레임 간 100ms
  .SetWidth(1.0f)
  .Play()
```

### 재생 제어

`Play()`, `Pause()`, `Stop()`, `JumpTo()`로 애니메이션 재생을 제어합니다:

```cpp
AnimatedImageVisual visual = AnimatedImageVisual::New()
  .SetResourceUrl("animation.gif")
  .Play();

// 나중에 애니메이션 일시 정지
visual.Pause();

// 재생 재개
visual.Play();

// 정지 및 재설정
visual.Stop();

// 특정 프레임으로 이동
visual.JumpTo(10);
```

### 루프 구성

애니메이션이 반복되는 횟수를 설정합니다:

```cpp
AnimatedImageVisual::New()
  .SetResourceUrl("animation.gif")
  .SetLoopCount(3)  // 3번 재생
```

무한 루프에는 `-1`을 사용합니다.

### 속도 제어

`SetFrameSpeedFactor()`로 재생 속도를 조정합니다:

```cpp
AnimatedImageVisual::New()
  .SetResourceUrl("animation.gif")
  .SetFrameSpeedFactor(2.0f)  // 2배 속도
  .Play()
```

## ColorVisual

`ColorVisual`은 선택적 블러 및 컷아웃 효과가 있는 단색을 렌더링합니다.

### 기본 사용법

```cpp
ColorVisual::New()
  .SetColor(UiColor(0xFF0000))  // 빨간색
  .SetWidth(1.0f)
  .SetHeight(1.0f)
```

### 블러 효과

색상에 블러 효과를 적용합니다:

```cpp
ColorVisual::New()
  .SetColor(UiColor(0x3F0F0F).WithAlpha(0.2f))
  .SetBlurRadius(12_spx)
  .SetWidth(1.0f)
  .SetHeight(1.0f)
```

### 컷아웃 정책

비주얼이 뷰의 모양과 상호작용하는 방식을 제어합니다:

```cpp
ColorVisual::New()
  .SetColor(UiColor(0x000000).WithAlpha(0.5f))
  .SetCutoutPolicy(CutoutPolicy::CUTOUT_VIEW_WITH_CORNER_RADIUS)
```

사용 가능한 컷아웃 정책:

- `CUTOUT_VIEW_WITH_CORNER_RADIUS` - 모서리 반경이 있는 뷰 영역 잘라내기
- `CUTOUT_OUTSIDE_WITH_CORNER_RADIUS` - 뷰 영역 외부 잘라내기

### 그림자 효과

블러와 컷아웃 정책을 결합하여 그림자 효과를 만듭니다:

```cpp
View view = View::New()
  .SetCornerRadius(0.25f)
  .SetCornerRadiusPolicyRelative()
  .Visuals(
    Visual::ContainerRangeType::BETWEEN_BACKGROUND_EFFECT_AND_BACKGROUND,
    {
      ColorVisual::New()
        .SetName("Shadow")
        .SetOffsetX(10_spx)
        .SetOffsetY(10_spx)
        .SetWidth(1.0f)
        .SetHeight(1.0f)
        .SetProportionFlags(Visual::Transform::ProportionFlags::SIZE_PROPORTIONAL)
        .SetBlurRadius(12_spx)
        .SetCornerRadius(12_spx)
        .SetColor(UiColor(0x000000).WithAlpha(0.3f))
        .SetCutoutPolicy(CutoutPolicy::CUTOUT_VIEW_WITH_CORNER_RADIUS),
    });
```
