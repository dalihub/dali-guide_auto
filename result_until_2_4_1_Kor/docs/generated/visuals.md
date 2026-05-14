---
title: 비주얼
sidebar_label: 비주얼
category: images-visuals
---

# 비주얼 {#visuals}

비주얼은 색상 채우기, 이미지, 애니메이션 이미지 시퀀스, Lottie 애니메이션을 그리기 위해 `Dali::Ui::View`에 연결하는 재사용 가능한 `dali-ui` 콘텐츠 객체입니다.

## 목차 {#table-of-contents}

- [뷰에 비주얼 연결](#attach-visuals-to-a-view)
- [정적 이미지 그리기](#draw-static-images)
- [색상 레이어와 부드러운 효과 추가](#add-color-layers-and-soft-effects)
- [애니메이션 이미지 시퀀스 재생](#play-animated-image-sequences)
- [Lottie 애니메이션 비주얼 사용](#use-lottie-animation-visuals)
- [비주얼 검사 및 순서 변경](#inspect-and-reorder-visuals)

## 뷰에 비주얼 연결 {#attach-visuals-to-a-view}

비주얼은 `Dali::Ui::View`가 소유하는 애플리케이션용 콘텐츠입니다. `Dali::Ui::ImageVisual`, `Dali::Ui::ColorVisual`, `Dali::Ui::AnimatedImageVisual`, `Dali::Ui::LottieAnimationVisual` 같은 구체적인 비주얼을 만든 다음 `AddVisual`로 뷰에 추가합니다.

`Dali::Ui::Visual::ContainerRangeType`은 뷰의 배경 효과, 배경, 콘텐츠, 장식, 전경 효과 범위를 기준으로 비주얼이 그려질 위치를 제어합니다.

```cpp
void AddPanelImage(Dali::Ui::View panel)
{
  Dali::Ui::ImageVisual visual =
    Dali::Ui::ImageVisual::New()
      .SetResourceUrl(Dali::String("/opt/usr/apps/org.example/res/panel.png"))
      .SetDesiredWidth(480)
      .SetDesiredHeight(270)
      .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO)
      .SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX_THEN_LINEAR)
      .SetEnableBrokenImage(true);

  panel.AddVisual(
    visual,
    Dali::Ui::Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT);
}
```

선언형으로 뷰를 구성할 때 `Dali::Ui::View::Visuals`는 `Dali::Ui::VisualBase` 객체의 초기화 목록을 컨테이너 범위에 추가합니다. 비주얼을 추가할 수 없으면 assertion이 발생하므로, 중복이나 용량 오류를 명시적으로 처리해야 할 때는 `AddVisual`을 사용합니다.

```cpp
Dali::Ui::View MakeCard()
{
  return Dali::Ui::View::New()
    .Visuals(
      Dali::Ui::Visual::ContainerRangeType::BETWEEN_BACKGROUND_EFFECT_AND_BACKGROUND,
      {
        Dali::Ui::ColorVisual::New()
          .SetName(Dali::String("card-shadow"))
          .SetColor(Dali::Ui::UiColor(0x000000).WithAlpha(0.22f))
          .SetBlurRadius(18.0f)
          .SetExtraWidth(24.0f)
          .SetExtraHeight(24.0f)
      })
    .Visuals(
      Dali::Ui::Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT,
      {
        Dali::Ui::ImageVisual::New()
          .SetName(Dali::String("card-image"))
          .SetResourceUrl(Dali::String("/opt/usr/apps/org.example/res/card.png"))
          .SetFittingMode(Dali::Ui::Image::FittingMode::FILL)
      });
}
```

## 정적 이미지 그리기 {#draw-static-images}

단일 이미지 리소스에는 `Dali::Ui::ImageVisual`을 사용합니다. `URL`은 필수 이미지 입력이며, `SetResourceUrl`, `SetDesiredWidth`, `SetDesiredHeight`, `SetFittingMode`, `SetSamplingMode` 같은 타입 지정 setter로 일반적인 이미지 로딩 및 샘플링 동작을 구성합니다.

```cpp
Dali::Ui::ImageVisual MakeThumbnailVisual()
{
  return Dali::Ui::ImageVisual::New()
    .SetName(Dali::String("thumbnail"))
    .SetResourceUrl(Dali::String("/opt/usr/apps/org.example/res/thumb.jpg"))
    .SetDesiredWidth(160)
    .SetDesiredHeight(160)
    .SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO)
    .SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX)
    .SetLoadPolicy(Dali::Ui::Image::LoadPolicy::ATTACHED)
    .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DETACHED)
    .SetOrientationCorrection(true);
}
```

`Dali::Ui::ImageVisual::Property`는 `Dali::Ui::ImageVisual::Property::URL`, `Dali::Ui::ImageVisual::Property::PIXEL_AREA`, `Dali::Ui::ImageVisual::Property::ALPHA_MASK_URL`, `Dali::Ui::ImageVisual::Property::BORDER` 등 이미지 전용 속성 상수를 소유합니다. 일반 애플리케이션 코드에서는 타입 지정 setter를 우선 사용하고, 범용 `Dali::Ui::VisualBase` 경로에서 인덱스 기반 접근이 필요할 때 속성 상수를 사용합니다.

```cpp
void ConfigureImageThroughVisualBase(Dali::Ui::VisualBase visual)
{
  visual.SetProperty(
    Dali::Ui::ImageVisual::Property::PIXEL_AREA,
    Dali::Vector4(0.0f, 0.0f, 0.5f, 1.0f));

  visual.SetProperty(
    Dali::Ui::ImageVisual::Property::ALPHA_MASK_URL,
    Dali::String("/opt/usr/apps/org.example/res/mask.png"));
}
```

크기 조절 가능한 N-patch 리소스에서는 `Dali::Ui::ImageVisual::Property::BORDER`, `Dali::Ui::ImageVisual::Property::BORDER_ONLY`, `Dali::Ui::ImageVisual::Property::AUXILIARY_IMAGE`, `Dali::Ui::ImageVisual::Property::AUXILIARY_IMAGE_ALPHA`가 `Dali::Ui::ImageVisual::Property`에 속합니다.

```cpp
Dali::Ui::ImageVisual MakeNinePatchBackground()
{
  return Dali::Ui::ImageVisual::New()
    .SetResourceUrl(Dali::String("/opt/usr/apps/org.example/res/panel.9.png"))
    .SetNPatchBorder(Dali::Extents(12u, 12u, 12u, 12u))
    .SetNPatchBorderOnly(false)
    .SetNPatchAuxiliaryImage(Dali::String("/opt/usr/apps/org.example/res/panel-overlay.png"))
    .SetNPatchAuxiliaryImageAlpha(0.35f);
}
```

## 색상 레이어와 부드러운 효과 추가 {#add-color-layers-and-soft-effects}

단색 채우기, 오버레이, 부드러운 그림자, `Dali::Ui::View`에 연결되는 컷아웃 효과에는 `Dali::Ui::ColorVisual`을 사용합니다. `Dali::Ui::ColorVisual::Property`는 `Dali::Ui::ColorVisual::Property::BLUR_RADIUS`와 `Dali::Ui::ColorVisual::Property::CUTOUT_POLICY`를 소유합니다.

```cpp
void AddSoftScrim(Dali::Ui::View content)
{
  Dali::Ui::ColorVisual scrim =
    Dali::Ui::ColorVisual::New()
      .SetName(Dali::String("scrim"))
      .SetColor(Dali::Ui::UiColor(0x000000).WithAlpha(0.32f))
      .SetBlurRadius(0.0f)
      .SetWidth(1.0f)
      .SetHeight(1.0f)
      .SetProportionFlags(Dali::Ui::Visual::Transform::ProportionFlags::SIZE_PROPORTIONAL);

  content.AddVisual(
    scrim,
    Dali::Ui::Visual::ContainerRangeType::BETWEEN_DECORATION_AND_FOREGROUND_EFFECT);
}
```

그림자나 클리핑된 오버레이 효과에는 `SetBlurRadius`와 `SetCutoutPolicy`를 함께 사용합니다. `CutoutPolicy` 열거형은 코너 반경을 포함한 뷰 영역에 적용하는 `CUTOUT_VIEW_WITH_CORNER_RADIUS`와 코너 반경을 포함한 바깥 영역에 적용하는 `CUTOUT_OUTSIDE_WITH_CORNER_RADIUS`를 제공합니다.

```cpp
Dali::Ui::ColorVisual MakeInnerShadow()
{
  using Dali::Ui::CutoutPolicy;

  return Dali::Ui::ColorVisual::New()
    .SetName(Dali::String("inner-shadow"))
    .SetColor(Dali::Ui::UiColor(0x000000).WithAlpha(0.0f))
    .SetBlurRadius(32.0f)
    .SetBorderlineWidth(20.0f)
    .SetBorderlineColor(Dali::Ui::UiColor(0x000000).WithAlpha(0.45f))
    .SetBorderlineOffset(1.0f)
    .SetCornerRadius(18.0f)
    .SetCutoutPolicy(CutoutPolicy::CUTOUT_OUTSIDE_WITH_CORNER_RADIUS);
}
```

## 애니메이션 이미지 시퀀스 재생 {#play-animated-image-sequences}

애니메이션 이미지 리소스나 이미지 URL 시퀀스에는 `Dali::Ui::AnimatedImageVisual`을 사용합니다. `SetResourceUrl`과 `SetResourceUrlList`는 상호 배타적입니다. 단일 리소스에는 `SetResourceUrl`을 사용하고, 프레임 시퀀스에는 `SetResourceUrlList`를 사용합니다. `SetFrameDelay`는 여러 URL로 구성된 시퀀스에 적용됩니다.

```cpp
Dali::Ui::AnimatedImageVisual MakeLoadingSpinner()
{
  return Dali::Ui::AnimatedImageVisual::New()
    .SetResourceUrl(Dali::String("/opt/usr/apps/org.example/res/loading.webp"))
    .SetDesiredWidth(96)
    .SetDesiredHeight(96)
    .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO)
    .SetLoopCount(-1)
    .SetFrameSpeedFactor(1.0f)
    .SetEnableBrokenImage(true)
    .Play();
}
```

```cpp
Dali::Ui::AnimatedImageVisual MakeFrameSequence()
{
  return Dali::Ui::AnimatedImageVisual::New()
    .SetResourceUrlList({
      Dali::String("/opt/usr/apps/org.example/res/progress-00.png"),
      Dali::String("/opt/usr/apps/org.example/res/progress-01.png"),
      Dali::String("/opt/usr/apps/org.example/res/progress-02.png")
    })
    .SetFrameDelay(80)
    .SetBatchSize(2)
    .SetCacheSize(4)
    .SetLoopCount(3)
    .SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::LAST_FRAME)
    .Play();
}
```

`Dali::Ui::AnimatedImageVisual::Property`는 `Dali::Ui::AnimatedImageVisual::Property::FRAME_DELAY`, `Dali::Ui::AnimatedImageVisual::Property::BATCH_SIZE`, `Dali::Ui::AnimatedImageVisual::Property::CACHE_SIZE`, `Dali::Ui::AnimatedImageVisual::Property::LOOP_COUNT`, `Dali::Ui::AnimatedImageVisual::Property::PLAY_STATE`, `Dali::Ui::AnimatedImageVisual::Property::CURRENT_FRAME_NUMBER`, `Dali::Ui::AnimatedImageVisual::Property::TOTAL_FRAME_NUMBER` 같은 애니메이션 전용 속성 상수를 소유합니다.

```cpp
void PauseAndInspect(Dali::Ui::AnimatedImageVisual visual)
{
  visual.Pause();

  int currentFrame = visual.GetCurrentFrameNumber();
  int totalFrames = visual.GetTotalFrameNumber();
  Dali::Ui::AnimatedImage::PlayState state = visual.GetPlayState();

  if(totalFrames > 0 && state == Dali::Ui::AnimatedImage::PlayState::PAUSED)
  {
    int nextFrame = (currentFrame + 1) % totalFrames;
    visual.JumpTo(nextFrame);
  }
}
```

## Lottie 애니메이션 비주얼 사용 {#use-lottie-animation-visuals}

Lottie JSON 애니메이션 파일에는 `Dali::Ui::LottieAnimationVisual`을 사용합니다. 이 비주얼은 `Dali::Ui::ImageVisual`과 공통 이미지 로딩 속성을 공유하며, `SetLoopCount`, `SetPlayRange`, `SetStopBehavior`, `SetFrameSpeedFactor` 같은 재생 속성도 제공합니다.

```cpp
Dali::Ui::LottieAnimationVisual MakeSuccessAnimation()
{
  return Dali::Ui::LottieAnimationVisual::New()
    .SetResourceUrl(Dali::String("/opt/usr/apps/org.example/res/success.json"))
    .SetDesiredWidth(240)
    .SetDesiredHeight(240)
    .SetLoopCount(1)
    .SetFrameSpeedFactor(1.0f)
    .SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::LAST_FRAME)
    .SetEnableFrameCache(true)
    .SetRenderScale(1.0f)
    .Play();
}
```

`Dali::Ui::LottieAnimationVisual::Property`는 `Dali::Ui::LottieAnimationVisual::Property::ENABLE_FRAME_CACHE`, `Dali::Ui::LottieAnimationVisual::Property::RENDER_SCALE`, `Dali::Ui::LottieAnimationVisual::Property::CONTENT_INFO`, `Dali::Ui::LottieAnimationVisual::Property::MARKER_INFO` 같은 Lottie 전용 상수를 소유합니다. `CONTENT_INFO`와 `MARKER_INFO`는 읽기 전용 조회 데이터이므로 값을 설정하지 말고 `GetContentInfo`, `GetMarkerInfo` 또는 인덱스 기반 속성 읽기로 가져옵니다.

```cpp
void UseLottieMarkers(Dali::Ui::LottieAnimationVisual visual)
{
  Dali::Property::Map markerInfo = visual.GetMarkerInfo();
  Dali::Property::Map contentInfo = visual.GetContentInfo();

  visual.SetPlayRange(Dali::Property::Array()
    .Add(Dali::String("intro"))
    .Add(Dali::String("complete")));

  visual.Play();
}
```

프레임을 직접 제어하려면 `Dali::Ui::LottieAnimationVisual`에서 `Play`, `Pause`, `Stop`, `JumpTo`를 사용합니다.

```cpp
void RestartLottie(Dali::Ui::LottieAnimationVisual visual)
{
  visual.Stop()
        .JumpTo(0)
        .Play();
}
```

## 비주얼 검사 및 순서 변경 {#inspect-and-reorder-visuals}

모든 구체적인 비주얼은 `Dali::Ui::VisualBase`입니다. 비주얼을 연결한 뒤 `GetOwner`는 소유자인 `Dali::Ui::View`를 반환하고, `GetContainerRangeType`은 비주얼 컨테이너를 반환하며, `GetSiblingOrder`는 해당 컨테이너 안에서의 순서를 반환합니다.

```cpp
void MoveTopVisualToFront(Dali::Ui::View view)
{
  auto range = Dali::Ui::Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT;
  uint32_t count = view.GetVisualCount(range);

  if(count == 0u)
  {
    return;
  }

  Dali::Ui::VisualBase visual = view.GetVisualAt(range, count - 1u);
  visual.RaiseToTop();
}
```

`Dali::Ui::VisualBase`는 한 번에 하나의 뷰 컨테이너에만 속할 수 있습니다. 같은 비주얼을 다른 `Dali::Ui::View`나 다른 컨테이너 범위에 추가하면 이전 소유자 또는 범위에서 새 위치로 이동합니다. `RaiseToTop`, `LowerToBottom`, `RaiseAbove`, `LowerBelow` 같은 형제 순서 API는 같은 컨테이너 범위 안에서만 비주얼 순서를 변경합니다. 비주얼을 명시적으로 제거하려면 소유 뷰에서 `RemoveVisual`을 호출하거나 비주얼에서 `Detach`를 호출합니다.

```cpp
void MoveVisual(Dali::Ui::View source, Dali::Ui::View target)
{
  auto range = Dali::Ui::Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT;

  if(source.GetVisualCount(range) == 0u)
  {
    return;
  }

  Dali::Ui::VisualBase visual = source.GetVisualAt(range, 0u);

  target.AddVisual(
    visual,
    Dali::Ui::Visual::ContainerRangeType::BETWEEN_DECORATION_AND_FOREGROUND_EFFECT);
}
```
