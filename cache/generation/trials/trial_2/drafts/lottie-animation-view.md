---
title: Lottie Animation View
sidebar_label: Lottie Animation View
category: views-components
---

# Lottie Animation View

`Dali::Ui::LottieAnimationView` displays JSON-based Lottie vector animations in a dali-ui `View` tree and provides playback, loop, frame-range, loading, and rasterization controls.

## Table of Contents

- [Create and Display a Lottie Animation](#create-and-display-a-lottie-animation)
- [Control Playback and Looping](#control-playback-and-looping)
- [Play a Frame Range](#play-a-frame-range)
- [React to Loading and Completion](#react-to-loading-and-completion)
- [Tune Loading and Resource Lifetime](#tune-loading-and-resource-lifetime)
- [Adjust Rendering Quality and Appearance](#adjust-rendering-quality-and-appearance)
- [Inspect Content and State](#inspect-content-and-state)
- [Property Constants](#property-constants)

## Create and Display a Lottie Animation

Use `Dali::Ui::LottieAnimationView::New()` to create a view from a Lottie JSON URL. The returned object is a `Dali::Ui::View`, so application code can add it to the same dali-ui view hierarchy as other components.

```cpp
#include <dali-ui-foundation/public-api/lottie-animation-view.h>

using namespace Dali;
using namespace Dali::Ui;

LottieAnimationView CreateLoadingAnimation()
{
  LottieAnimationView lottie =
    LottieAnimationView::New("animations/loading.json")
      .SetDesiredWidth(240)
      .SetDesiredHeight(240)
      .SetLoopCount(-1);

  return lottie;
}
```

`SetResourceUrl()` changes the JSON resource after construction, and `GetResourceUrl()` returns the current URL.

```cpp
LottieAnimationView lottie = LottieAnimationView::New();

lottie
  .SetResourceUrl("animations/success.json")
  .SetLoopCount(1)
  .Play();

Dali::String currentUrl = lottie.GetResourceUrl();
```

When a generic `View` handle is passed through app code, use `Dali::Ui::LottieAnimationView::DownCast()` before calling Lottie-specific APIs.

```cpp
void ConfigureIfLottie(View view)
{
  LottieAnimationView lottie = LottieAnimationView::DownCast(view);
  if(lottie)
  {
    lottie.SetFrameSpeedFactor(1.0f);
  }
}
```

## Control Playback and Looping

`Play()`, `Pause()`, `Stop()`, and `JumpToFrame()` control playback directly on `Dali::Ui::LottieAnimationView`.

```cpp
void StartLooping(LottieAnimationView lottie)
{
  lottie
    .SetLoopCount(-1)
    .SetFrameSpeedFactor(1.0f)
    .Play();
}

void PauseAtFrame(LottieAnimationView lottie, int frame)
{
  lottie
    .Pause()
    .JumpToFrame(frame);
}

void StopAtFirstFrame(LottieAnimationView lottie)
{
  lottie
    .SetStopBehavior(Ui::AnimatedImage::StopBehavior::FIRST_FRAME)
    .Stop();
}
```

`SetLoopCount()` accepts `-1` for infinite looping, `0` for no playback, and positive values for an exact loop count. Use `SetLoopingMode()` to choose whether each loop restarts from the beginning or auto-reverses.

```cpp
void ConfigureCelebration(LottieAnimationView lottie)
{
  lottie
    .SetLoopCount(3)
    .SetLoopingMode(Ui::LottieAnimation::LoopingMode::AUTO_REVERSE)
    .SetFrameSpeedFactor(1.5f)
    .Play();
}
```

## Play a Frame Range

Use `SetMinMaxFrame()` when the app already knows frame numbers, for example after reading `GetTotalFrame()` when the resource is ready.

```cpp
void PlayFirstHalf(LottieAnimationView lottie)
{
  int totalFrame = lottie.GetTotalFrame();
  if(totalFrame > 0)
  {
    lottie
      .SetMinMaxFrame(0, totalFrame / 2)
      .SetLoopCount(1)
      .Play();
  }
}
```

If the Lottie file contains markers, use `SetMinMaxFrameByMarker()` to select a semantic range by marker name.

```cpp
void PlaySuccessMarker(LottieAnimationView lottie)
{
  lottie
    .SetMinMaxFrameByMarker("success")
    .SetLoopCount(1)
    .Play();
}

void PlayBetweenMarkers(LottieAnimationView lottie)
{
  lottie
    .SetMinMaxFrameByMarker("intro", "outro")
    .SetLoopingMode(Ui::LottieAnimation::LoopingMode::RESTART)
    .Play();
}
```

## React to Loading and Completion

`ResourceReadySignal()` is emitted when the animation resource is ready. The signal callback receives the ready `View`, which can be downcast to `Dali::Ui::LottieAnimationView` before reading Lottie-specific state.

```cpp
class LottieController : public ConnectionTracker
{
public:
  void Setup()
  {
    mLottie = LottieAnimationView::New("animations/progress.json")
                .SetLoopCount(-1);

    mLottie.ResourceReadySignal().Connect(this, &LottieController::OnResourceReady);
    mLottie.AnimationFinishedSignal().Connect(this, &LottieController::OnAnimationFinished);
  }

  void OnResourceReady(View view)
  {
    LottieAnimationView lottie = LottieAnimationView::DownCast(view);
    if(lottie && lottie.GetLoadingStatus() == Ui::Visual::ResourceStatus::READY)
    {
      lottie.Play();
    }
  }

  void OnAnimationFinished(View view)
  {
    LottieAnimationView lottie = LottieAnimationView::DownCast(view);
    if(lottie)
    {
      lottie.JumpToFrame(0);
    }
  }

private:
  LottieAnimationView mLottie;
};
```

`AnimationFinishedSignal()` is emitted when playback completes all configured loops. Use it for one-shot confirmation animations or for advancing UI state after a Lottie sequence completes.

## Tune Loading and Resource Lifetime

`SetPlaceholderUrl()` shows a placeholder image while the Lottie resource is loading. This is useful when the view should occupy its layout area before the JSON animation is available.

```cpp
LottieAnimationView CreateDeferredLottie()
{
  LottieAnimationView lottie =
    LottieAnimationView::New()
      .SetPlaceholderUrl("images/placeholder.png")
      .SetLoopCount(-1);

  lottie.SetResourceUrl("animations/waiting.json");
  return lottie;
}
```

Use `SetSynchronousLoading()` only for cases where blocking load behavior is acceptable for the app flow. `SetReleasePolicy()` controls when the animation resource can be released.

```cpp
void ConfigureLoadingPolicy(LottieAnimationView lottie)
{
  lottie
    .SetSynchronousLoading(false)
    .SetReleasePolicy(Ui::Image::ReleasePolicy::DETACHED)
    .SetPreMultipliedAlpha(true);
}

bool IsConfiguredForSynchronousLoad(LottieAnimationView lottie)
{
  return lottie.IsSynchronousLoading();
}
```

The release policy values follow the image resource policy model: `Ui::Image::ReleasePolicy::DETACHED`, `Ui::Image::ReleasePolicy::DESTROYED`, and `Ui::Image::ReleasePolicy::NEVER`.

## Adjust Rendering Quality and Appearance

Use `SetDesiredWidth()` and `SetDesiredHeight()` as rasterization size hints. Use `SetRenderScale()` when the animation should rasterize at a different scale than the view size.

```cpp
LottieAnimationView CreateHighDensityIcon()
{
  return LottieAnimationView::New("animations/icon.json")
    .SetDesiredWidth(96)
    .SetDesiredHeight(96)
    .SetRenderScale(2.0f)
    .SetLoopCount(1);
}
```

`SetEnableFrameCache()` can reduce repeated rasterization work during looping by caching frames in memory. `SetRedrawOnScaleDown()` and `SetRedrawOnScaleUp()` control whether the animation redraws during scaling. `SetNotifyAfterRasterization()` enables notification after rasterization work completes.

```cpp
void ConfigurePerformance(LottieAnimationView lottie)
{
  lottie
    .SetEnableFrameCache(true)
    .SetRedrawOnScaleDown(true)
    .SetRedrawOnScaleUp(true)
    .SetNotifyAfterRasterization(false);
}
```

Use `SetImageColor()` to multiply the rendered animation by a single RGBA color. This is an overall tint; for per-layer animated values, use `SetDynamicProperty()` with a `Ui::LottieAnimation::DynamicPropertyInfo`.

```cpp
void TintAnimation(LottieAnimationView lottie)
{
  lottie.SetImageColor(UiColor(0x66CCFFFF));

  UiColor currentTint = lottie.GetImageColor();
}
```

`SetDepthIndex()` adjusts the visual depth index used by the Lottie animation view.

```cpp
void BringAnimationForward(LottieAnimationView lottie)
{
  lottie.SetDepthIndex(10);
}
```

## Inspect Content and State

Use state getters to drive UI controls such as play/pause buttons, frame labels, or scrubbers.

```cpp
void UpdatePlaybackUi(LottieAnimationView lottie)
{
  Ui::AnimatedImage::PlayState state = lottie.GetPlayState();
  int currentFrame = lottie.GetCurrentFrame();
  int totalFrame = lottie.GetTotalFrame();

  if(state == Ui::AnimatedImage::PlayState::PAUSED && totalFrame > 0)
  {
    lottie.JumpToFrame(currentFrame);
  }
}
```

`GetContentInfo()` returns layer information from the Lottie file, and `GetMarkerInfo()` returns marker information. Both return a `Dali::Property::Map` keyed by names from the Lottie resource.

```cpp
void ReadLottieMetadata(LottieAnimationView lottie)
{
  Dali::Property::Map contentInfo = lottie.GetContentInfo();
  Dali::Property::Map markerInfo = lottie.GetMarkerInfo();

  bool hasLayerInfo = !contentInfo.Empty();
  bool hasMarkerInfo = !markerInfo.Empty();
}
```

Configuration getters are useful for preserving app settings across view recreation.

```cpp
struct LottieSettings
{
  int loopCount;
  float speed;
  float renderScale;
  Ui::AnimatedImage::StopBehavior stopBehavior;
  Ui::Image::ReleasePolicy releasePolicy;
};

LottieSettings CaptureSettings(LottieAnimationView lottie)
{
  LottieSettings settings;
  settings.loopCount = lottie.GetLoopCount();
  settings.speed = lottie.GetFrameSpeedFactor();
  settings.renderScale = lottie.GetRenderScale();
  settings.stopBehavior = lottie.GetStopBehavior();
  settings.releasePolicy = lottie.GetReleasePolicy();
  return settings;
}
```

## Property Constants

Prefer typed setters such as `SetResourceUrl()`, `SetLoopCount()`, and `SetRenderScale()` in application code. `Dali::Ui::LottieAnimationView::Property` owns the property constants for tooling or generic property-based code paths.

| Property constant | Meaning |
| --- | --- |
| `Dali::Ui::LottieAnimationView::Property::IMAGE` | Lottie JSON resource URL |
| `Dali::Ui::LottieAnimationView::Property::LOOP_COUNT` | Number of loops, with `-1` for infinite looping |
| `Dali::Ui::LottieAnimationView::Property::LOOPING_MODE` | `Ui::LottieAnimation::LoopingMode` value |
| `Dali::Ui::LottieAnimationView::Property::STOP_BEHAVIOR` | `Ui::AnimatedImage::StopBehavior` value |
| `Dali::Ui::LottieAnimationView::Property::FRAME_SPEED_FACTOR` | Playback speed multiplier |
| `Dali::Ui::LottieAnimationView::Property::IMAGE_COLOR` | RGBA color multiplier |
| `Dali::Ui::LottieAnimationView::Property::DESIRED_WIDTH` | Rasterization width hint |
| `Dali::Ui::LottieAnimationView::Property::DESIRED_HEIGHT` | Rasterization height hint |
| `Dali::Ui::LottieAnimationView::Property::RELEASE_POLICY` | `Ui::Image::ReleasePolicy` value |
| `Dali::Ui::LottieAnimationView::Property::SYNCHRONOUS_LOADING` | Synchronous loading flag |
| `Dali::Ui::LottieAnimationView::Property::PRE_MULTIPLIED_ALPHA` | Pre-multiplied alpha flag |
| `Dali::Ui::LottieAnimationView::Property::REDRAW_IN_SCALING_DOWN` | Redraw while scaling down |
| `Dali::Ui::LottieAnimationView::Property::REDRAW_IN_SCALING_UP` | Redraw while scaling up |
| `Dali::Ui::LottieAnimationView::Property::ENABLE_FRAME_CACHE` | Frame cache flag |
| `Dali::Ui::LottieAnimationView::Property::NOTIFY_AFTER_RASTERIZATION` | Notify after rasterization flag |
| `Dali::Ui::LottieAnimationView::Property::RENDER_SCALE` | Rasterization scale multiplier |
| `Dali::Ui::LottieAnimationView::Property::PLACEHOLDER_IMAGE` | Placeholder image URL |

`Dali::Ui::LottieAnimationViewPropertyIndex` defines the underlying index range and values used by `Dali::Ui::LottieAnimationView::Property`, including `PropertyRange::PROPERTY_START_INDEX` and `PropertyRange::PROPERTY_END_INDEX`. For application code, use the `Dali::Ui::LottieAnimationView::Property` owner when a property constant is needed.
