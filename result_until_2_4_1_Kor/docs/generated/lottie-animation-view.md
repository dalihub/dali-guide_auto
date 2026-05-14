---
title: Lottie 애니메이션 뷰
sidebar_label: Lottie 애니메이션 뷰
category: views-components
---

# Lottie 애니메이션 뷰 {#lottie-animation-view}

`Dali::Ui::LottieAnimationView`는 JSON 기반 Lottie 벡터 애니메이션을 표시하고 dali-ui 뷰 트리 안에서 재생 제어 기능을 제공합니다.

## 목차 {#table-of-contents}

- [Lottie 애니메이션 뷰 생성](#create-a-lottie-animation-view)
- [재생 제어](#control-playback)
- [반복, 속도, 정지 동작](#looping-speed-and-stop-behavior)
- [프레임 범위와 마커](#frame-ranges-and-markers)
- [로딩, 플레이스홀더, 리소스 정책](#loading-placeholder-and-resource-policy)
- [래스터화와 시각적 조정](#rasterization-and-visual-tuning)
- [시그널과 상태 조회](#signals-and-state-queries)
- [콘텐츠와 마커 메타데이터 확인](#inspect-content-and-marker-metadata)

## Lottie 애니메이션 뷰 생성 {#create-a-lottie-animation-view}

`Dali::Ui::LottieAnimationView::New`를 사용해 뷰를 생성합니다. URL은 생성 시점에 지정하거나 나중에 `Dali::Ui::LottieAnimationView::SetResourceUrl`로 설정할 수 있습니다.

`Dali::Ui::LottieAnimationView`는 dali-ui `Dali::Ui::View`이므로, 애플리케이션 코드는 이를 원시 액터처럼 다루기보다 뷰 트리의 일부로 유지해야 합니다.

```cpp
#include <dali-ui-foundation/public-api/lottie-animation-view.h>

Dali::Ui::LottieAnimationView CreateLoadingAnimation()
{
  return Dali::Ui::LottieAnimationView::New("assets/loading.json")
    .SetDesiredWidth(240)
    .SetDesiredHeight(240)
    .SetLoopCount(-1)
    .SetFrameSpeedFactor(1.0f);
}
```

`Dali::Ui::LottieAnimationView::SetDesiredWidth`와 `Dali::Ui::LottieAnimationView::SetDesiredHeight`는 래스터화 크기 힌트를 픽셀 단위로 설정합니다. 레이아웃 트리에서 뷰 크기를 지정해야 할 때는 `Dali::Ui::View::SetRequestedWidth`, `Dali::Ui::View::SetRequestedHeight` 같은 레이아웃 API를 사용하세요.

뷰를 생성한 뒤 애니메이션 파일을 변경하려면 `Dali::Ui::LottieAnimationView::SetResourceUrl`을 호출합니다.

```cpp
void ShowSuccessAnimation(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView
    .Stop()
    .SetResourceUrl("assets/success.json")
    .SetLoopCount(1)
    .Play();
}
```

애플리케이션 로직에서 현재 구성을 다시 읽어야 할 때는 `Dali::Ui::LottieAnimationView::GetResourceUrl`, `Dali::Ui::LottieAnimationView::GetDesiredWidth`, `Dali::Ui::LottieAnimationView::GetDesiredHeight`를 사용합니다.

```cpp
bool UsesCompactAsset(Dali::Ui::LottieAnimationView lottieView)
{
  return lottieView.GetResourceUrl() == Dali::String("assets/compact-spinner.json") &&
         lottieView.GetDesiredWidth() <= 128 &&
         lottieView.GetDesiredHeight() <= 128;
}
```

`Dali::Ui::LottieAnimationView::Property`에는 이 뷰의 속성 상수가 정의되어 있습니다. 이 상수들은 `Dali::Ui::LottieAnimationViewPropertyIndex`의 별칭이며, 해당 타입은 이 기능의 속성 인덱스 범위, 속성 이름, 속성 타입을 정의합니다. 애플리케이션 코드에서는 `Dali::Ui::LottieAnimationView::SetLoopCount`, `Dali::Ui::LottieAnimationView::SetFrameSpeedFactor` 같은 타입 지정 setter를 우선 사용하세요.

## 재생 제어 {#control-playback}

`Dali::Ui::LottieAnimationView::Play`, `Dali::Ui::LottieAnimationView::Pause`, `Dali::Ui::LottieAnimationView::Stop`은 재생을 제어합니다. `Dali::Ui::LottieAnimationView::JumpToFrame`은 애니메이션을 특정 프레임으로 이동합니다.

```cpp
void StartIntro(Dali::Ui::LottieAnimationView introView)
{
  introView
    .SetResourceUrl("assets/intro.json")
    .SetLoopCount(1)
    .JumpToFrame(0)
    .Play();
}

void PauseIntro(Dali::Ui::LottieAnimationView introView)
{
  introView.Pause();
}

void ResetIntro(Dali::Ui::LottieAnimationView introView)
{
  introView
    .Stop()
    .JumpToFrame(0);
}
```

자체 컨트롤에 재생 상태를 반영하려면 `Dali::Ui::LottieAnimationView::GetPlayState`, `Dali::Ui::LottieAnimationView::GetCurrentFrame`, `Dali::Ui::LottieAnimationView::GetTotalFrame`을 사용합니다. 실제 비주얼을 사용할 수 있기 전에는 이 조회 함수들이 정지된 애니메이션과 프레임 수 `0`을 보고합니다.

```cpp
bool IsPastHalfway(Dali::Ui::LottieAnimationView lottieView)
{
  const int totalFrame = lottieView.GetTotalFrame();
  if(totalFrame <= 0)
  {
    return false;
  }

  return lottieView.GetCurrentFrame() > totalFrame / 2;
}

bool IsPlaying(Dali::Ui::LottieAnimationView lottieView)
{
  return lottieView.GetPlayState() == Dali::Ui::AnimatedImage::PlayState::PLAYING;
}
```

## 반복, 속도, 정지 동작 {#looping-speed-and-stop-behavior}

`Dali::Ui::LottieAnimationView::SetLoopCount`는 재생을 몇 번 반복할지 제어합니다. `-1`은 무한 반복, `0`은 애니메이션을 재생하지 않음을 의미하며, 양수 값은 정확한 반복 횟수를 지정합니다.

```cpp
Dali::Ui::LottieAnimationView CreateSpinner()
{
  return Dali::Ui::LottieAnimationView::New("assets/spinner.json")
    .SetLoopCount(-1)
    .SetLoopingMode(Dali::Ui::LottieAnimation::LoopingMode::RESTART)
    .Play();
}
```

`Dali::Ui::LottieAnimationView::SetLoopingMode`는 `Dali::Ui::LottieAnimation::LoopingMode::RESTART` 또는 `Dali::Ui::LottieAnimation::LoopingMode::AUTO_REVERSE`와 함께 사용합니다.

```cpp
void UseReverseLoop(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView
    .SetLoopCount(3)
    .SetLoopingMode(Dali::Ui::LottieAnimation::LoopingMode::AUTO_REVERSE)
    .Play();
}
```

`Dali::Ui::LottieAnimationView::SetFrameSpeedFactor`는 재생 속도를 변경합니다. `0.0f`와 `1.0f` 사이의 값은 애니메이션을 느리게 만들고, `1.0f`보다 큰 값은 더 빠르게 만듭니다.

```cpp
void SetPreviewSpeed(Dali::Ui::LottieAnimationView lottieView, bool fastPreview)
{
  lottieView.SetFrameSpeedFactor(fastPreview ? 2.0f : 0.5f);
}
```

`Dali::Ui::LottieAnimationView::SetStopBehavior`는 `Dali::Ui::LottieAnimationView::Stop` 이후 어떤 프레임을 계속 표시할지 제어합니다.

```cpp
void ConfigureStopFrame(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView.SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::LAST_FRAME);
}

void RestoreDefaultStopFrame(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView.SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::CURRENT_FRAME);
}
```

활성 값을 읽으려면 `Dali::Ui::LottieAnimationView::GetLoopCount`, `Dali::Ui::LottieAnimationView::GetLoopingMode`, `Dali::Ui::LottieAnimationView::GetFrameSpeedFactor`, `Dali::Ui::LottieAnimationView::GetStopBehavior`를 사용합니다.

## 프레임 범위와 마커 {#frame-ranges-and-markers}

애플리케이션에서 재생할 프레임 번호를 알고 있을 때는 `Dali::Ui::LottieAnimationView::SetMinMaxFrame`을 사용합니다. 하나의 Lottie 파일에서 선택한 프레임 범위만 재생할 때 유용합니다.

```cpp
void PlayFirstHalf(Dali::Ui::LottieAnimationView lottieView)
{
  const int totalFrame = lottieView.GetTotalFrame();
  if(totalFrame > 0)
  {
    lottieView
      .SetMinMaxFrame(0, totalFrame / 2)
      .JumpToFrame(0)
      .Play();
  }
}

void PlaySecondHalf(Dali::Ui::LottieAnimationView lottieView)
{
  const int totalFrame = lottieView.GetTotalFrame();
  if(totalFrame > 0)
  {
    lottieView
      .SetMinMaxFrame(totalFrame / 2, totalFrame - 1)
      .JumpToFrame(totalFrame / 2)
      .Play();
  }
}
```

Lottie 파일에 이름이 지정된 마커가 들어 있다면 `Dali::Ui::LottieAnimationView::SetMinMaxFrameByMarker`를 사용합니다. 마커 하나를 전달하면 해당 마커 범위를 재생합니다. 마커 두 개를 전달하면 첫 번째 마커의 시작부터 두 번째 마커의 끝까지 재생합니다.

```cpp
void PlayMarkerRange(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView
    .SetMinMaxFrameByMarker("pressed")
    .Play();
}

void PlayBetweenMarkers(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView
    .SetMinMaxFrameByMarker("open", "close")
    .Play();
}
```

## 로딩, 플레이스홀더, 리소스 정책 {#loading-placeholder-and-resource-policy}

`Dali::Ui::LottieAnimationView::SetPlaceholderUrl`은 Lottie 리소스를 로드하는 동안 표시할 이미지를 설정합니다. 이후 `Dali::Ui::LottieAnimationView::SetResourceUrl`을 사용해 애니메이션을 로드하거나 다시 로드할 수 있습니다.

```cpp
Dali::Ui::LottieAnimationView CreateRemoteStateAnimation()
{
  return Dali::Ui::LottieAnimationView::New()
    .SetPlaceholderUrl("assets/placeholder.png")
    .SetResourceUrl("assets/state.json")
    .SetLoopCount(-1);
}
```

`Dali::Ui::LottieAnimationView::SetSynchronousLoading`은 JSON 리소스를 이벤트 스레드에서 동기적으로 로드할지 제어합니다. 일반적인 UI 흐름에서는 비활성화한 상태로 두고, 애플리케이션에서 동기 로딩 동작이 명시적으로 필요할 때만 활성화하세요.

```cpp
void ConfigureLoadingMode(Dali::Ui::LottieAnimationView lottieView, bool synchronous)
{
  lottieView.SetSynchronousLoading(synchronous);

  if(lottieView.IsSynchronousLoading())
  {
    lottieView.SetLoopCount(1);
  }
}
```

`Dali::Ui::LottieAnimationView::SetReleasePolicy`는 애니메이션 텍스처를 캐시에서 언제 해제할지 제어합니다. 일반적인 장면 분리 시 해제 정책에는 `Dali::Ui::Image::ReleasePolicy::DETACHED`를 사용하고, 비주얼이 파괴될 때 해제하려면 `Dali::Ui::Image::ReleasePolicy::DESTROYED`를 사용합니다. `Dali::Ui::Image::ReleasePolicy::NEVER`는 애니메이션을 반드시 캐시에 유지해야 할 때만 사용하세요.

```cpp
void KeepSuccessAnimationCached(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView
    .SetResourceUrl("assets/success.json")
    .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::NEVER);
}

void UseDefaultResourceRelease(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView.SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DETACHED);
}
```

관련 getter는 `Dali::Ui::LottieAnimationView::GetPlaceholderUrl`, `Dali::Ui::LottieAnimationView::GetReleasePolicy`, `Dali::Ui::LottieAnimationView::IsSynchronousLoading`, `Dali::Ui::LottieAnimationView::GetLoadingStatus`, `Dali::Ui::LottieAnimationView::GetResourceUrl`입니다.

## 래스터화와 시각적 조정 {#rasterization-and-visual-tuning}

`Dali::Ui::LottieAnimationView::SetRenderScale`은 비주얼 크기를 기준으로 래스터화 배율을 변경합니다. 값이 클수록 더 선명한 결과를 만들 수 있지만 메모리 비용이 증가합니다. 음수 값은 이미지를 뒤집습니다.

```cpp
void ConfigureHighDensityRendering(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView
    .SetRenderScale(2.0f)
    .SetEnableFrameCache(true);
}
```

`Dali::Ui::LottieAnimationView::SetEnableFrameCache`는 래스터화된 프레임을 캐시해 반복 중 같은 프레임을 다시 래스터화하는 일을 줄입니다. 메모리 비용이 허용되는 경우 자주 반복되는 애니메이션에 사용하세요.

```cpp
void ConfigureLoopingBadge(Dali::Ui::LottieAnimationView badgeView)
{
  badgeView
    .SetLoopCount(-1)
    .SetEnableFrameCache(true)
    .Play();
}
```

`Dali::Ui::LottieAnimationView::SetRedrawOnScaleDown`과 `Dali::Ui::LottieAnimationView::SetRedrawOnScaleUp`은 뷰가 스케일될 때의 다시 그리기 동작을 제어합니다. `Dali::Ui::LottieAnimationView::SetNotifyAfterRasterization`은 래스터화 이후 알림을 보낼지 제어합니다.

```cpp
void ConfigureScalingBehavior(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView
    .SetRedrawOnScaleDown(true)
    .SetRedrawOnScaleUp(true)
    .SetNotifyAfterRasterization(false);
}
```

`Dali::Ui::LottieAnimationView::SetImageColor`는 렌더링된 애니메이션에 RGBA 색상 배율을 적용합니다. 렌더링 결과가 미리 곱해진 알파를 사용해야 할 때는 `Dali::Ui::LottieAnimationView::SetPreMultipliedAlpha`를 사용합니다.

```cpp
void DimAnimation(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView
    .SetImageColor(Dali::Ui::UiColor(1.0f, 1.0f, 1.0f, 0.55f))
    .SetPreMultipliedAlpha(true);
}
```

`Dali::Ui::LottieAnimationView::SetPixelArea`는 `(x, y, width, height)`로 표현되는 애니메이션의 정규화된 하위 영역을 표시합니다.

```cpp
void ShowTopHalf(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 1.0f, 0.5f));
}
```

이 설정들을 읽으려면 `Dali::Ui::LottieAnimationView::GetRenderScale`, `Dali::Ui::LottieAnimationView::IsFrameCacheEnabled`, `Dali::Ui::LottieAnimationView::IsRedrawOnScaleDown`, `Dali::Ui::LottieAnimationView::IsRedrawOnScaleUp`, `Dali::Ui::LottieAnimationView::IsNotifyAfterRasterizationEnabled`, `Dali::Ui::LottieAnimationView::GetImageColor`, `Dali::Ui::LottieAnimationView::IsPreMultipliedAlpha`, `Dali::Ui::LottieAnimationView::GetPixelArea`를 사용합니다.

## 시그널과 상태 조회 {#signals-and-state-queries}

`Dali::Ui::LottieAnimationView::ResourceReadySignal`은 애니메이션 리소스 로딩이 끝났을 때 발생합니다. `Dali::Ui::LottieAnimationView::AnimationFinishedSignal`은 구성된 모든 반복 재생이 완료되었을 때 발생합니다. 두 시그널 콜백 모두 시그널을 발생시킨 `Dali::Ui::View`를 받습니다.

```cpp
class LottieController : public Dali::ConnectionTracker
{
public:
  void SetView(Dali::Ui::LottieAnimationView lottieView)
  {
    mLottieView = lottieView;

    mLottieView.ResourceReadySignal().Connect(
      this,
      &LottieController::OnResourceReady);

    mLottieView.AnimationFinishedSignal().Connect(
      this,
      &LottieController::OnAnimationFinished);
  }

private:
  void OnResourceReady(Dali::Ui::View view)
  {
    Dali::Ui::LottieAnimationView lottieView =
      Dali::Ui::LottieAnimationView::DownCast(view);

    if(lottieView)
    {
      const int totalFrame = lottieView.GetTotalFrame();
      if(totalFrame > 0)
      {
        lottieView.JumpToFrame(0);
      }
    }
  }

  void OnAnimationFinished(Dali::Ui::View view)
  {
    Dali::Ui::LottieAnimationView lottieView =
      Dali::Ui::LottieAnimationView::DownCast(view);

    if(lottieView)
    {
      lottieView.Stop();
    }
  }

  Dali::Ui::LottieAnimationView mLottieView;
};
```

콜백이나 뷰 트리 헬퍼가 기본 `Dali::Ui::View`를 제공하는 경우 `Dali::Ui::LottieAnimationView::DownCast`가 유용합니다.

```cpp
void RestartIfLottie(Dali::Ui::View view)
{
  Dali::Ui::LottieAnimationView lottieView =
    Dali::Ui::LottieAnimationView::DownCast(view);

  if(lottieView)
  {
    lottieView
      .JumpToFrame(0)
      .Play();
  }
}
```

## 콘텐츠와 마커 메타데이터 확인 {#inspect-content-and-marker-metadata}

`Dali::Ui::LottieAnimationView::GetContentInfo`는 로드된 Lottie 파일의 레이어 정보를 반환합니다. `Dali::Ui::LottieAnimationView::GetMarkerInfo`는 마커 정보를 반환합니다. 반환되는 각 값은 키가 이름이고 값이 프레임 범위인 `Dali::Property::Map`입니다.

```cpp
bool HasLottieMetadata(Dali::Ui::LottieAnimationView lottieView)
{
  Dali::Property::Map contentInfo = lottieView.GetContentInfo();
  Dali::Property::Map markerInfo  = lottieView.GetMarkerInfo();

  return contentInfo.Count() > 0u || markerInfo.Count() > 0u;
}
```

애니메이션 파일이 애플리케이션 상태용 이름 있는 세그먼트를 정의한다면 마커 메타데이터를 `Dali::Ui::LottieAnimationView::SetMinMaxFrameByMarker`와 함께 사용하세요.

```cpp
void PlayReadySegment(Dali::Ui::LottieAnimationView lottieView)
{
  Dali::Property::Map markerInfo = lottieView.GetMarkerInfo();

  if(markerInfo.Count() > 0u)
  {
    lottieView
      .SetMinMaxFrameByMarker("ready")
      .SetLoopCount(1)
      .Play();
  }
}
```

고급 프레임별 변경에는 `Dali::Ui::LottieAnimationView::SetDynamicProperty`가 `Dali::Ui::LottieAnimation::DynamicPropertyInfo`를 받습니다. 호출 후 `Dali::Ui::LottieAnimation::DynamicPropertyInfo::callback`의 소유권은 비주얼로 이전되며, 콜백은 작업자 스레드에서 호출됩니다. 따라서 해당 콜백 안에서는 DALi API를 호출하지 마세요.

```cpp
void ApplyDynamicProperty(
  Dali::Ui::LottieAnimationView lottieView,
  const Dali::Ui::LottieAnimation::DynamicPropertyInfo& propertyInfo)
{
  lottieView.SetDynamicProperty(propertyInfo);
}
```
