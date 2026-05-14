---
title: 렌더 효과
sidebar_label: 렌더 효과
category: images-visuals
---

# 렌더 효과

렌더 효과는 `Dali::Ui::View` 객체에 적용할 수 있는 실시간 시각적 효과를 제공합니다. dali-ui 프레임워크는 통합된 `RenderEffect` 인터페이스를 통해 블러 효과와 마스킹 기능을 제공합니다.

## 목차

- [뷰에 렌더 효과 적용](#뷰에-렌더-효과-적용)
- [가우시안 블러 효과](#가우시안-블러-효과)
- [배경 블러 효과](#배경-블러-효과)
- [마스크 효과](#마스크-효과)
- [블러 효과 애니메이션](#블러-효과-애니메이션)

## 뷰에 렌더 효과 적용

모든 렌더 효과는 `Dali::Ui::RenderEffect`를 상속합니다. 뷰는 한 번에 최대 하나의 렌더 효과를 가질 수 있습니다. `SetRenderEffect()`로 효과를 적용하고 `ClearRenderEffect()`로 제거합니다.

```cpp
// 뷰 생성
Ui::View view = Ui::View::New();
view.SetRequestedWidth(200.0f);
view.SetRequestedHeight(200.0f);
view.SetBackgroundColor(UiColor(0x3A6D75));

// 렌더 효과 적용
Ui::GaussianBlurEffect blurEffect = Ui::GaussianBlurEffect::New(60u);
view.SetRenderEffect(blurEffect);

// 효과 제거
view.ClearRenderEffect();
```

효과는 뷰에 설정되면 자동으로 활성화됩니다. `Activate()`와 `Deactivate()`로 수동으로 활성화를 제어할 수 있습니다:

```cpp
Ui::GaussianBlurEffect effect = Ui::GaussianBlurEffect::New(30u);
view.SetRenderEffect(effect);

// 일시적으로 효과 비활성화
effect.Deactivate();

// 효과 다시 활성화
effect.Activate();

// 활성화 상태 확인
if (effect.IsActivated())
{
  // 효과가 활성 상태
}
```

## 가우시안 블러 효과

`Dali::Ui::GaussianBlurEffect`는 소유자 뷰와 모든 자식에 가우시안 블러를 적용합니다. 서리 유리 효과나 포커스 기반 시각적 상태를 만드는 데 유용합니다.

### 가우시안 블러 효과 생성

기본 반경 10픽셀로 생성:

```cpp
Ui::GaussianBlurEffect blurEffect = Ui::GaussianBlurEffect::New();
```

또는 커스텀 블러 반경을 지정:

```cpp
Ui::GaussianBlurEffect blurEffect = Ui::GaussianBlurEffect::New(60u);
```

### 블러 속성 구성

`SetBlurRadius()`로 블러 강도를 제어합니다:

```cpp
Ui::GaussianBlurEffect blurEffect = Ui::GaussianBlurEffect::New(30u);

// 블러 반경 증가
blurEffect.SetBlurRadius(80u);

// 현재 반경 가져오기
uint32_t currentRadius = blurEffect.GetBlurRadius();
```

다운스케일 팩터로 성능을 최적화합니다. 낮은 값은 렌더링 품질을 낮추지만 성능을 향상시킵니다:

```cpp
blurEffect.SetBlurDownscaleFactor(0.25f);  // 1/4 해상도로 렌더링
float factor = blurEffect.GetBlurDownscaleFactor();
```

## 배경 블러 효과

`Dali::Ui::BackgroundBlurEffect`는 뷰의 배경에 블러를 적용하여 콘텐츠는 선명하게 유지합니다.

```cpp
Ui::BackgroundBlurEffect bgBlur = Ui::BackgroundBlurEffect::New(30u);
view.SetRenderEffect(bgBlur);
```

## 마스크 효과

`Dali::Ui::MaskEffect`는 마스크 이미지를 사용하여 뷰의 모양을 정의합니다.

```cpp
Ui::MaskEffect maskEffect = Ui::MaskEffect::New("mask.png");
view.SetRenderEffect(maskEffect);
```

## 블러 효과 애니메이션

블러 반경을 애니메이션하여 동적인 효과를 만듭니다:

```cpp
Ui::GaussianBlurEffect blurEffect = Ui::GaussianBlurEffect::New(10u);
view.SetRenderEffect(blurEffect);

// 블러 애니메이션
Dali::Animation animation = Dali::Animation::New(1.0f);
animation.AnimateTo(Property(blurEffect, Ui::GaussianBlurEffect::Property::BLUR_RADIUS), 50.0f);
animation.Play();
```
