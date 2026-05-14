---
title: 애니메이션 이미지 뷰
sidebar_label: 애니메이션 이미지 뷰
category: views-components
---

# 애니메이션 이미지 뷰 {#animated-image-view}

`Dali::Ui::AnimatedImageView`는 dali-ui 애플리케이션에서 GIF 또는 WebP 파일 같은 애니메이션 이미지 리소스를 표시하며, 뷰 수준에서 재생, 로딩, 마스킹, 프레임 제어 기능을 제공합니다.

## 목차 {#table-of-contents}

- [애니메이션 이미지 뷰 만들기](#create-an-animated-image-view)
- [이미지 소스 선택하기](#choose-the-image-source)
- [재생 및 프레임 제어하기](#control-playback-and-frames)
- [로딩 및 렌더링 조정하기](#tune-loading-and-rendering)
- [마스크, 플레이스홀더 및 픽셀 영역 사용하기](#use-masks-placeholders-and-pixel-areas)
- [리소스 및 애니메이션 시그널 처리하기](#handle-resource-and-animation-signals)
- [프로퍼티 상수](#property-constants)

## 애니메이션 이미지 뷰 만들기 {#create-an-animated-image-view}

`Dali::Ui::AnimatedImageView::New`를 사용하여 뷰를 생성합니다. 선택 사항인 URL 매개변수는 초기 애니메이션 이미지 리소스를 설정합니다. 이 객체는 `Dali::Ui::View`이므로, 애플리케이션 코드는 원시 액터로 다루지 말고 dali-ui 뷰 트리 안에 유지해야 합니다.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

Dali::Ui::AnimatedImageView CreateBusyIndicator()
{
  return Dali::Ui::AnimatedImageView::New("images/busy.gif")
    .SetLoopCount(-1)
    .SetFrameSpeedFactor(1.0f)
    .SetBatchSize(2)
    .SetCacheSize(6);
}
```

`SetLoopCount(-1)`은 무한 재생을 구성합니다. `SetFrameSpeedFactor(1.0f)`는 파일의 기본 재생 속도를 유지합니다. `SetBatchSize`는 각 배치에서 미리 로드할 프레임 수를 설정하고, `SetCacheSize`는 캐시에 유지할 프레임 수를 설정합니다.

## 이미지 소스 선택하기 {#choose-the-image-source}

`Dali::Ui::AnimatedImageView`는 `SetResourceUrl`을 통해 단일 애니메이션 이미지 URL을 사용할 수도 있고, `SetResourceUrls`를 통해 정지 이미지 시퀀스를 사용할 수도 있습니다. URL 시퀀스는 애니메이션이 번호가 매겨진 이미지 프레임으로 제공될 때 유용합니다.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

void UseSingleAnimatedFile(Dali::Ui::AnimatedImageView view)
{
  view
    .SetResourceUrl("images/spinner.webp")
    .SetLoopCount(-1)
    .Play();
}
```

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

void UseFrameSequence(Dali::Ui::AnimatedImageView view)
{
  Dali::Vector<Dali::String> frames;
  frames.PushBack("images/loading_001.png");
  frames.PushBack("images/loading_002.png");
  frames.PushBack("images/loading_003.png");

  view
    .SetResourceUrls(frames)
    .SetFrameDelay(120)
    .SetBatchSize(3)
    .SetCacheSize(6)
    .Play();
}
```

`SetResourceUrl`을 설정하면 모든 URL 시퀀스가 지워지고, `SetResourceUrls`를 설정하면 단일 URL이 지워집니다. 뷰가 단일 URL로 구성된 경우에는 `GetResourceUrl`을 사용하고, 프레임 시퀀스로 구성된 경우에는 `GetResourceUrls`를 사용합니다.

```cpp
Dali::String currentUrl = view.GetResourceUrl();
const Dali::Vector<Dali::String>& currentFrames = view.GetResourceUrls();
```

## 재생 및 프레임 제어하기 {#control-playback-and-frames}

재생은 `Play`, `Pause`, `Stop`, `JumpToFrame`으로 제어합니다. 현재 애니메이션 상태를 기반으로 애플리케이션 UI나 로그를 갱신하려면 `GetPlayState`, `GetCurrentFrame`, `GetTotalFrame`을 사용합니다.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

void StartPreview(Dali::Ui::AnimatedImageView view)
{
  view
    .SetLoopCount(3)
    .SetFrameSpeedFactor(0.5f)
    .Play();
}

void PausePreview(Dali::Ui::AnimatedImageView view)
{
  view.Pause();

  Dali::Ui::AnimatedImage::PlayState state = view.GetPlayState();
  int currentFrame = view.GetCurrentFrame();
  int totalFrames = view.GetTotalFrame();

  (void)state;
  (void)currentFrame;
  (void)totalFrames;
}

void ShowFrame(Dali::Ui::AnimatedImageView view, int frame)
{
  view.JumpToFrame(frame);
}
```

`SetStopBehavior`는 `Stop` 이후 표시할 프레임을 제어합니다. 애플리케이션에서 선택한 `Dali::Ui::AnimatedImage::StopBehavior` 값을 전달합니다.

```cpp
void ConfigureStopFrame(
  Dali::Ui::AnimatedImageView view,
  Dali::Ui::AnimatedImage::StopBehavior stopBehavior)
{
  view
    .SetStopBehavior(stopBehavior)
    .Stop();
}
```

## 로딩 및 렌더링 조정하기 {#tune-loading-and-rendering}

로딩 및 렌더링 구성에는 타입이 지정된 setter를 사용합니다. `SetDesiredWidth`와 `SetDesiredHeight`는 픽셀 단위의 디코딩 크기 힌트를 제공합니다. `SetImageLoadWithViewSize`는 현재 뷰 크기에 맞춰 로드하도록 요청합니다. `SetSynchronousLoading`은 애플리케이션에서 명시적으로 필요한 경우 동기 로딩을 선택합니다.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

void ConfigureLoading(Dali::Ui::AnimatedImageView view)
{
  view
    .SetDesiredWidth(256)
    .SetDesiredHeight(256)
    .SetImageLoadWithViewSize(true)
    .SetSynchronousLoading(false);
}
```

특정 스케일링 정책으로 애니메이션을 뷰 영역에 맞춰야 할 때는 `SetFittingMode`와 `SetSamplingMode`를 사용합니다. 이미지 색상 보정과 알파 처리는 `SetImageColor`와 `SetPreMultipliedAlpha`를 사용합니다.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

void ConfigureRendering(
  Dali::Ui::AnimatedImageView view,
  Dali::Ui::Image::FittingMode fittingMode,
  Dali::Ui::Image::SamplingMode samplingMode,
  const Dali::Ui::UiColor& tint)
{
  view
    .SetFittingMode(fittingMode)
    .SetSamplingMode(samplingMode)
    .SetImageColor(tint)
    .SetPreMultipliedAlpha(true);
}
```

리소스 수명 주기는 애플리케이션에서 사용하는 이미지 정책에 맞춰 `SetLoadPolicy`와 `SetReleasePolicy`로 구성합니다.

```cpp
void ConfigureResourcePolicy(
  Dali::Ui::AnimatedImageView view,
  Dali::Ui::Image::LoadPolicy loadPolicy,
  Dali::Ui::Image::ReleasePolicy releasePolicy)
{
  view
    .SetLoadPolicy(loadPolicy)
    .SetReleasePolicy(releasePolicy);
}
```

## 마스크, 플레이스홀더 및 픽셀 영역 사용하기 {#use-masks-placeholders-and-pixel-areas}

`SetPlaceholderUrl`은 애니메이션 리소스가 로드되는 동안 표시할 이미지를 구성합니다. `SetAlphaMaskUrl`, `SetCropToMask`, `SetMaskingMode`는 애니메이션 이미지에 알파 마스크를 적용합니다.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

void ConfigureMaskedAnimation(
  Dali::Ui::AnimatedImageView view,
  Dali::Ui::Image::MaskingType maskingMode)
{
  view
    .SetResourceUrl("images/avatar.gif")
    .SetPlaceholderUrl("images/avatar-placeholder.png")
    .SetAlphaMaskUrl("images/avatar-mask.png")
    .SetCropToMask(true)
    .SetMaskingMode(maskingMode)
    .Play();
}
```

애니메이션 이미지의 정규화된 하위 영역을 표시하려면 `SetPixelArea`를 사용합니다. 값은 `(x, y, width, height)`이며, 각 구성 요소의 범위는 `[0, 1]`입니다.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>
#include <dali/public-api/math/vector4.h>

void ShowUpperLeftQuarter(Dali::Ui::AnimatedImageView view)
{
  view
    .SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.5f, 0.5f))
    .Play();

  Dali::Vector4 area = view.GetPixelArea();
  (void)area;
}
```

## 리소스 및 애니메이션 시그널 처리하기 {#handle-resource-and-animation-signals}

이미지 리소스 로드가 완료되어 표시할 준비가 되었을 때 반응하려면 `ResourceReadySignal`을 사용합니다. 구성된 루프 횟수만큼 재생이 완료되었을 때 반응하려면 `AnimationFinishedSignal`을 사용합니다. 두 시그널 모두 `Dali::Ui::View` 콜백 매개변수를 사용합니다.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

class AnimatedImageController
{
public:
  void Initialize()
  {
    mView = Dali::Ui::AnimatedImageView::New("images/intro.gif")
      .SetLoopCount(1);

    mView.ResourceReadySignal().Connect(this, &AnimatedImageController::OnResourceReady);
    mView.AnimationFinishedSignal().Connect(this, &AnimatedImageController::OnAnimationFinished);
  }

  void Start()
  {
    mView.Play();
  }

private:
  void OnResourceReady(Dali::Ui::View view)
  {
    Dali::Ui::AnimatedImageView animatedView = Dali::Ui::AnimatedImageView::DownCast(view);
    int totalFrames = animatedView.GetTotalFrame();
    (void)totalFrames;
  }

  void OnAnimationFinished(Dali::Ui::View view)
  {
    Dali::Ui::AnimatedImageView animatedView = Dali::Ui::AnimatedImageView::DownCast(view);
    animatedView.JumpToFrame(0);
  }

private:
  Dali::Ui::AnimatedImageView mView;
};
```

`GetLoadingStatus`는 애니메이션 이미지의 현재 비주얼 리소스 상태를 반환합니다.

```cpp
Dali::Ui::Visual::ResourceStatus status = view.GetLoadingStatus();
(void)status;
```

## 프로퍼티 상수 {#property-constants}

애플리케이션 코드에서는 `SetResourceUrl`, `SetLoopCount`, `SetFrameDelay`, `SetAlphaMaskUrl`, `SetPixelArea` 같은 타입이 지정된 메서드를 우선 사용합니다. 앱에서 사용할 수 있는 프로퍼티 상수는 `Dali::Ui::AnimatedImageView::Property`가 소유합니다.

일반적인 `Dali::Ui::AnimatedImageView::Property` 상수는 다음과 같습니다.

- `Dali::Ui::AnimatedImageView::Property::IMAGE`
- `Dali::Ui::AnimatedImageView::Property::IMAGE_URLS`
- `Dali::Ui::AnimatedImageView::Property::LOOP_COUNT`
- `Dali::Ui::AnimatedImageView::Property::STOP_BEHAVIOR`
- `Dali::Ui::AnimatedImageView::Property::FRAME_SPEED_FACTOR`
- `Dali::Ui::AnimatedImageView::Property::BATCH_SIZE`
- `Dali::Ui::AnimatedImageView::Property::CACHE_SIZE`
- `Dali::Ui::AnimatedImageView::Property::FRAME_DELAY`
- `Dali::Ui::AnimatedImageView::Property::IMAGE_COLOR`
- `Dali::Ui::AnimatedImageView::Property::PRE_MULTIPLIED_ALPHA`
- `Dali::Ui::AnimatedImageView::Property::FITTING_MODE`
- `Dali::Ui::AnimatedImageView::Property::SAMPLING_MODE`
- `Dali::Ui::AnimatedImageView::Property::DESIRED_WIDTH`
- `Dali::Ui::AnimatedImageView::Property::DESIRED_HEIGHT`
- `Dali::Ui::AnimatedImageView::Property::LOAD_POLICY`
- `Dali::Ui::AnimatedImageView::Property::RELEASE_POLICY`
- `Dali::Ui::AnimatedImageView::Property::SYNCHRONOUS_LOADING`
- `Dali::Ui::AnimatedImageView::Property::IMAGE_LOAD_WITH_VIEW_SIZE`
- `Dali::Ui::AnimatedImageView::Property::ALPHA_MASK_URL`
- `Dali::Ui::AnimatedImageView::Property::CROP_TO_MASK`
- `Dali::Ui::AnimatedImageView::Property::MASKING_MODE`
- `Dali::Ui::AnimatedImageView::Property::PLACEHOLDER_IMAGE`
- `Dali::Ui::AnimatedImageView::Property::PIXEL_AREA`

`Dali::Ui::AnimatedImageViewPropertyIndex`는 `Dali::Ui::AnimatedImageView`의 기반 프로퍼티 인덱스 범위를 정의합니다. 예를 들어 `Dali::Ui::AnimatedImageViewPropertyIndex::ALPHA_MASK_URL`과 `Dali::Ui::AnimatedImageViewPropertyIndex::BATCH_SIZE`는 해당 `Dali::Ui::AnimatedImageView::Property` 항목 뒤에 있는 인덱스 정의입니다.
