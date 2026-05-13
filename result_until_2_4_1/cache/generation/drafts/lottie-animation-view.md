---
title: Lottie Animation View
sidebar_label: Lottie Animation View
category: views-components
---

# Lottie Animation View

`Dali::Ui::LottieAnimationView` displays and controls JSON-based Lottie animations in a dali-ui view tree.

## Table of Contents

- [Create a Lottie Animation View](#create-a-lottie-animation-view)
- [Control Playback](#control-playback)
- [Looping, Speed, and Stop Behavior](#looping-speed-and-stop-behavior)
- [Frame Ranges and Markers](#frame-ranges-and-markers)
- [Loading, Placeholder, and Resource Policy](#loading-placeholder-and-resource-policy)
- [Rasterization and Visual Tuning](#rasterization-and-visual-tuning)
- [Signals and State Queries](#signals-and-state-queries)
- [Inspect Content and Marker Metadata](#inspect-content-and-marker-metadata)

## Create a Lottie Animation View

Use `Dali::Ui::LottieAnimationView::New` to create the view. The URL can be supplied at construction time or later with `Dali::Ui::LottieAnimationView::SetResourceUrl`.

`Dali::Ui::LottieAnimationView` is a dali-ui `Dali::Ui::View`, so application code should keep it as part of the view tree rather than treating it as a raw actor.

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

To change the animation file after creating the view, call `Dali::Ui::LottieAnimationView::SetResourceUrl`.

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

Use `Dali::Ui::LottieAnimationView::GetResourceUrl`, `Dali::Ui::LottieAnimationView::GetDesiredWidth`, and `Dali::Ui::LottieAnimationView::GetDesiredHeight` when application logic needs to read back the current configuration.

```cpp
bool UsesCompactAsset(Dali::Ui::LottieAnimationView lottieView)
{
  return lottieView.GetResourceUrl() == Dali::String("assets/compact-spinner.json") &&
         lottieView.GetDesiredWidth() <= 128 &&
         lottieView.GetDesiredHeight() <= 128;
}
```

`Dali::Ui::LottieAnimationView::Property` owns the property constants for this view. The constants mirror `Dali::Ui::LottieAnimationViewPropertyIndex`, which defines the actual property index range and property names for the feature. Prefer typed setters such as `Dali::Ui::LottieAnimationView::SetLoopCount` and `Dali::Ui::LottieAnimationView::SetFrameSpeedFactor` in application code.

## Control Playback

`Dali::Ui::LottieAnimationView::Play`, `Dali::Ui::LottieAnimationView::Pause`, and `Dali::Ui::LottieAnimationView::Stop` control playback. `Dali::Ui::LottieAnimationView::JumpToFrame` moves the animation to a specific frame.

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

Use `Dali::Ui::LottieAnimationView::GetPlayState`, `Dali::Ui::LottieAnimationView::GetCurrentFrame`, and `Dali::Ui::LottieAnimationView::GetTotalFrame` to reflect playback state in your own controls.

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

## Looping, Speed, and Stop Behavior

`Dali::Ui::LottieAnimationView::SetLoopCount` controls how many times playback loops. `-1` means infinite looping, `0` means the animation will not play, and a positive value gives an exact loop count.

```cpp
Dali::Ui::LottieAnimationView CreateSpinner()
{
  return Dali::Ui::LottieAnimationView::New("assets/spinner.json")
    .SetLoopCount(-1)
    .SetLoopingMode(Dali::Ui::LottieAnimation::LoopingMode::RESTART)
    .Play();
}
```

Use `Dali::Ui::LottieAnimationView::SetLoopingMode` with `Dali::Ui::LottieAnimation::LoopingMode::RESTART` or `Dali::Ui::LottieAnimation::LoopingMode::AUTO_REVERSE`.

```cpp
void UseReverseLoop(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView
    .SetLoopCount(3)
    .SetLoopingMode(Dali::Ui::LottieAnimation::LoopingMode::AUTO_REVERSE)
    .Play();
}
```

`Dali::Ui::LottieAnimationView::SetFrameSpeedFactor` changes playback speed. A value below `1.0f` slows the animation, and a value above `1.0f` speeds it up.

```cpp
void SetPreviewSpeed(Dali::Ui::LottieAnimationView lottieView, bool fastPreview)
{
  lottieView.SetFrameSpeedFactor(fastPreview ? 2.0f : 0.5f);
}
```

`Dali::Ui::LottieAnimationView::SetStopBehavior` controls which frame remains visible after `Dali::Ui::LottieAnimationView::Stop`.

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

Read the active values with `Dali::Ui::LottieAnimationView::GetLoopCount`, `Dali::Ui::LottieAnimationView::GetLoopingMode`, `Dali::Ui::LottieAnimationView::GetFrameSpeedFactor`, and `Dali::Ui::LottieAnimationView::GetStopBehavior`.

## Frame Ranges and Markers

Use `Dali::Ui::LottieAnimationView::SetMinMaxFrame` when your application knows the frame numbers to play. This is useful for splitting one Lottie file into several application states.

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

Use `Dali::Ui::LottieAnimationView::SetMinMaxFrameByMarker` when the Lottie file contains named markers. Passing one marker plays that marker range. Passing two markers plays from the start of the first marker to the end of the second marker.

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

## Loading, Placeholder, and Resource Policy

`Dali::Ui::LottieAnimationView::SetPlaceholderUrl` sets an image to show while a Lottie resource is loading. `Dali::Ui::LottieAnimationView::SetResourceUrl` can then be used to load or reload the animation.

```cpp
Dali::Ui::LottieAnimationView CreateRemoteStateAnimation()
{
  return Dali::Ui::LottieAnimationView::New()
    .SetPlaceholderUrl("assets/placeholder.png")
    .SetResourceUrl("assets/state.json")
    .SetLoopCount(-1);
}
```

`Dali::Ui::LottieAnimationView::SetSynchronousLoading` controls whether the JSON resource is loaded synchronously. Keep it disabled for normal UI flow, and enable it only when your application explicitly needs synchronous loading behavior.

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

`Dali::Ui::LottieAnimationView::SetReleasePolicy` controls when cached resources can be released. Use `Dali::Ui::Image::ReleasePolicy::DETACHED` for the normal detached-from-scene policy, `Dali::Ui::Image::ReleasePolicy::DESTROYED` to keep resources until the visual is destroyed, and `Dali::Ui::Image::ReleasePolicy::NEVER` only when the animation must remain cached.

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

The related getters are `Dali::Ui::LottieAnimationView::GetPlaceholderUrl`, `Dali::Ui::LottieAnimationView::GetReleasePolicy`, `Dali::Ui::LottieAnimationView::IsSynchronousLoading`, `Dali::Ui::LottieAnimationView::GetLoadingStatus`, and `Dali::Ui::LottieAnimationView::GetResourceUrl`.

## Rasterization and Visual Tuning

`Dali::Ui::LottieAnimationView::SetRenderScale` changes the rasterization scale relative to the view size. Larger values can produce sharper output at higher memory cost.

```cpp
void ConfigureHighDensityRendering(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView
    .SetRenderScale(2.0f)
    .SetEnableFrameCache(true);
}
```

`Dali::Ui::LottieAnimationView::SetEnableFrameCache` caches rasterized frames to reduce repeated rasterization during loops. Use it for frequently looping animations when the memory cost is acceptable.

```cpp
void ConfigureLoopingBadge(Dali::Ui::LottieAnimationView badgeView)
{
  badgeView
    .SetLoopCount(-1)
    .SetEnableFrameCache(true)
    .Play();
}
```

`Dali::Ui::LottieAnimationView::SetRedrawOnScaleDown` and `Dali::Ui::LottieAnimationView::SetRedrawOnScaleUp` control redraw behavior when the view is scaled. `Dali::Ui::LottieAnimationView::SetNotifyAfterRasterization` enables resource-ready notification after rasterization.

```cpp
void ConfigureScalingBehavior(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView
    .SetRedrawOnScaleDown(true)
    .SetRedrawOnScaleUp(true)
    .SetNotifyAfterRasterization(false);
}
```

`Dali::Ui::LottieAnimationView::SetImageColor` applies an RGBA multiplier to the rendered animation. Use `Dali::Ui::LottieAnimationView::SetPreMultipliedAlpha` when the rendered output should use pre-multiplied alpha.

```cpp
void DimAnimation(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView
    .SetImageColor(Dali::Ui::UiColor(1.0f, 1.0f, 1.0f, 0.55f))
    .SetPreMultipliedAlpha(true);
}
```

`Dali::Ui::LottieAnimationView::SetPixelArea` displays a normalized sub-region of the animation, expressed as `(x, y, width, height)`.

```cpp
void ShowTopHalf(Dali::Ui::LottieAnimationView lottieView)
{
  lottieView.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 1.0f, 0.5f));
}
```

Use `Dali::Ui::LottieAnimationView::GetRenderScale`, `Dali::Ui::LottieAnimationView::IsFrameCacheEnabled`, `Dali::Ui::LottieAnimationView::IsRedrawOnScaleDown`, `Dali::Ui::LottieAnimationView::IsRedrawOnScaleUp`, `Dali::Ui::LottieAnimationView::IsNotifyAfterRasterizationEnabled`, `Dali::Ui::LottieAnimationView::GetImageColor`, `Dali::Ui::LottieAnimationView::IsPreMultipliedAlpha`, and `Dali::Ui::LottieAnimationView::GetPixelArea` to read these settings.

## Signals and State Queries

`Dali::Ui::LottieAnimationView::ResourceReadySignal` is emitted when loading completes. `Dali::Ui::LottieAnimationView::AnimationFinishedSignal` is emitted when playback completes all configured loops. Both signal callbacks receive the emitting `Dali::Ui::View`.

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

`Dali::Ui::LottieAnimationView::DownCast` is useful when a callback or view-tree helper gives you a base `Dali::Ui::View`.

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

## Inspect Content and Marker Metadata

`Dali::Ui::LottieAnimationView::GetContentInfo` returns layer information from the loaded Lottie file. `Dali::Ui::LottieAnimationView::GetMarkerInfo` returns marker information. Each returned value is a `Dali::Property::Map`.

```cpp
bool HasLottieMetadata(Dali::Ui::LottieAnimationView lottieView)
{
  Dali::Property::Map contentInfo = lottieView.GetContentInfo();
  Dali::Property::Map markerInfo  = lottieView.GetMarkerInfo();

  return contentInfo.Count() > 0u || markerInfo.Count() > 0u;
}
```

Use marker metadata together with `Dali::Ui::LottieAnimationView::SetMinMaxFrameByMarker` when the animation file defines named segments for application states.

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

For advanced per-frame changes, `Dali::Ui::LottieAnimationView::SetDynamicProperty` accepts a `Dali::Ui::LottieAnimation::DynamicPropertyInfo`. The callback is owned by the visual after the call, and it is invoked from a worker thread, so do not call DALi APIs from that callback.

```cpp
void ApplyDynamicProperty(
  Dali::Ui::LottieAnimationView lottieView,
  const Dali::Ui::LottieAnimation::DynamicPropertyInfo& propertyInfo)
{
  lottieView.SetDynamicProperty(propertyInfo);
}
```
