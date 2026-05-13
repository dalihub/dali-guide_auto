---
title: Lottie Animation View
sidebar_label: Lottie Animation View
category: views-components
---

# Lottie Animation View

`Dali::Ui::LottieAnimationView` displays and controls JSON-based Lottie vector animations in a dali-ui view tree.

## Table of Contents

- [Create a Lottie Animation View](#create-a-lottie-animation-view)
- [Control Playback](#control-playback)
- [Configure Looping, Speed, and Stop Behavior](#configure-looping-speed-and-stop-behavior)
- [Limit Playback to Frames or Markers](#limit-playback-to-frames-or-markers)
- [React to Loading and Completion](#react-to-loading-and-completion)
- [Tune Loading and Rasterization](#tune-loading-and-rasterization)
- [Adjust Appearance](#adjust-appearance)
- [Use Lottie Properties](#use-lottie-properties)

## Create a Lottie Animation View

Create the view with `Dali::Ui::LottieAnimationView::New`. Pass the Lottie JSON URL at construction time, or create an empty view and later call `Dali::Ui::LottieAnimationView::SetResourceUrl`.

```cpp
#include <dali-ui-foundation/public-api/lottie-animation-view.h>

Dali::Ui::LottieAnimationView CreateLoadingAnimation()
{
  return Dali::Ui::LottieAnimationView::New("animations/loading.json")
    .SetDesiredWidth(240)
    .SetDesiredHeight(240)
    .SetLoopCount(-1);
}
```

`Dali::Ui::LottieAnimationView` is a `Dali::Ui::View`, so it fits into a dali-ui view tree as the app-facing object. Keep animation-specific configuration on the typed `Dali::Ui::LottieAnimationView` handle, and treat `Dali::Ui::LottieAnimationView::SetDesiredWidth` and `Dali::Ui::LottieAnimationView::SetDesiredHeight` as rasterization size hints.

```cpp
Dali::Ui::LottieAnimationView animation =
  Dali::Ui::LottieAnimationView::New();

animation
  .SetResourceUrl("animations/success.json")
  .SetDesiredWidth(320)
  .SetDesiredHeight(320)
  .SetDepthIndex(1);
```

Use `Dali::Ui::LottieAnimationView::DownCast` when a generic handle is known to refer to a Lottie animation view.

```cpp
Dali::Ui::LottieAnimationView AsLottieView(Dali::BaseHandle handle)
{
  return Dali::Ui::LottieAnimationView::DownCast(handle);
}
```

## Control Playback

`Dali::Ui::LottieAnimationView::Play`, `Dali::Ui::LottieAnimationView::Pause`, and `Dali::Ui::LottieAnimationView::Stop` control playback directly. `Dali::Ui::LottieAnimationView::JumpToFrame` moves the animation to a specific frame.

```cpp
void Start(Dali::Ui::LottieAnimationView animation)
{
  animation.Play();
}

void PauseAtCurrentFrame(Dali::Ui::LottieAnimationView animation)
{
  animation.Pause();
}

void StopAtConfiguredFrame(Dali::Ui::LottieAnimationView animation)
{
  animation.Stop();
}

void PreviewFrame(Dali::Ui::LottieAnimationView animation, int frame)
{
  animation.JumpToFrame(frame);
}
```

Query playback state and frame position with `Dali::Ui::LottieAnimationView::GetPlayState`, `Dali::Ui::LottieAnimationView::GetCurrentFrame`, and `Dali::Ui::LottieAnimationView::GetTotalFrame`.

```cpp
bool IsAtEnd(Dali::Ui::LottieAnimationView animation)
{
  const int currentFrame = animation.GetCurrentFrame();
  const int totalFrame   = animation.GetTotalFrame();

  return totalFrame > 0 && currentFrame >= totalFrame - 1;
}

Dali::Ui::AnimatedImage::PlayState GetState(Dali::Ui::LottieAnimationView animation)
{
  return animation.GetPlayState();
}
```

## Configure Looping, Speed, and Stop Behavior

Set loop count with `Dali::Ui::LottieAnimationView::SetLoopCount`. A loop count of `-1` means infinite looping, `0` means the animation will not play, and a positive value is an exact loop count.

```cpp
Dali::Ui::LottieAnimationView CreateSpinner(const Dali::String& url)
{
  return Dali::Ui::LottieAnimationView::New(url)
    .SetLoopCount(-1)
    .SetFrameSpeedFactor(1.0f);
}
```

Use `Dali::Ui::LottieAnimationView::SetLoopingMode` to choose how each loop continues. `Dali::Ui::LottieAnimation::LoopingMode::RESTART` restarts from the beginning; `Dali::Ui::LottieAnimation::LoopingMode::AUTO_REVERSE` reverses direction at the end of a loop.

```cpp
void UsePingPongLoop(Dali::Ui::LottieAnimationView animation)
{
  animation
    .SetLoopCount(-1)
    .SetLoopingMode(Dali::Ui::LottieAnimation::LoopingMode::AUTO_REVERSE)
    .Play();
}
```

Use `Dali::Ui::LottieAnimationView::SetFrameSpeedFactor` for playback speed. Values below `1.0f` slow playback down; values above `1.0f` speed it up.

```cpp
void SetSlowMotion(Dali::Ui::LottieAnimationView animation)
{
  animation.SetFrameSpeedFactor(0.5f);
}

void SetFastPlayback(Dali::Ui::LottieAnimationView animation)
{
  animation.SetFrameSpeedFactor(2.0f);
}
```

Use `Dali::Ui::LottieAnimationView::SetStopBehavior` to choose which frame is shown after `Dali::Ui::LottieAnimationView::Stop`.

```cpp
void StopOnLastFrame(Dali::Ui::LottieAnimationView animation)
{
  animation
    .SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::LAST_FRAME)
    .Stop();
}
```

Read the active settings with `Dali::Ui::LottieAnimationView::GetLoopCount`, `Dali::Ui::LottieAnimationView::GetLoopingMode`, `Dali::Ui::LottieAnimationView::GetFrameSpeedFactor`, and `Dali::Ui::LottieAnimationView::GetStopBehavior`.

```cpp
float EffectiveSpeed(Dali::Ui::LottieAnimationView animation)
{
  return animation.GetFrameSpeedFactor();
}
```

## Limit Playback to Frames or Markers

Use `Dali::Ui::LottieAnimationView::SetMinMaxFrame` when the app already knows the frame range.

```cpp
void PlayIntroSegment(Dali::Ui::LottieAnimationView animation)
{
  animation
    .SetMinMaxFrame(0, 30)
    .SetLoopCount(1)
    .Play();
}
```

If the Lottie file includes markers, use `Dali::Ui::LottieAnimationView::SetMinMaxFrameByMarker`. Passing one marker plays that marker range. Passing two markers plays from the start of the first marker to the end of the second marker.

```cpp
void PlayNamedSegment(Dali::Ui::LottieAnimationView animation)
{
  animation
    .SetMinMaxFrameByMarker("success")
    .SetLoopCount(1)
    .Play();
}

void PlayMarkerSpan(Dali::Ui::LottieAnimationView animation)
{
  animation
    .SetMinMaxFrameByMarker("intro", "outro")
    .SetLoopCount(1)
    .Play();
}
```

Use `Dali::Ui::LottieAnimationView::GetContentInfo` and `Dali::Ui::LottieAnimationView::GetMarkerInfo` to inspect layer and marker ranges exposed by the Lottie content. Each returned map uses the content or marker name as the key and a two-element integer array for the start and end frames.

```cpp
Dali::Property::Map ReadMarkers(Dali::Ui::LottieAnimationView animation)
{
  return animation.GetMarkerInfo();
}

Dali::Property::Map ReadLayerRanges(Dali::Ui::LottieAnimationView animation)
{
  return animation.GetContentInfo();
}
```

## React to Loading and Completion

Connect `Dali::Ui::LottieAnimationView::ResourceReadySignal` to react when the resource is ready. The signal callback receives the ready `Dali::Ui::View`.

```cpp
class AnimationPresenter : public Dali::ConnectionTracker
{
public:
  void SetAnimation(Dali::Ui::LottieAnimationView animation)
  {
    mAnimation = animation;
    mAnimation.ResourceReadySignal().Connect(
      this,
      &AnimationPresenter::OnResourceReady);
  }

private:
  void OnResourceReady(Dali::Ui::View view)
  {
    Dali::Ui::LottieAnimationView animation =
      Dali::Ui::LottieAnimationView::DownCast(view);

    animation.Play();
  }

  Dali::Ui::LottieAnimationView mAnimation;
};
```

Connect `Dali::Ui::LottieAnimationView::AnimationFinishedSignal` to react after the animation completes all configured loops.

```cpp
class CompletionHandler : public Dali::ConnectionTracker
{
public:
  void Watch(Dali::Ui::LottieAnimationView animation)
  {
    mAnimation = animation;
    mAnimation.AnimationFinishedSignal().Connect(
      this,
      &CompletionHandler::OnAnimationFinished);
  }

private:
  void OnAnimationFinished(Dali::Ui::View view)
  {
    Dali::Ui::LottieAnimationView animation =
      Dali::Ui::LottieAnimationView::DownCast(view);

    animation.JumpToFrame(0);
  }

  Dali::Ui::LottieAnimationView mAnimation;
};
```

Use `Dali::Ui::LottieAnimationView::GetLoadingStatus` when the app needs to inspect the current visual resource state.

```cpp
Dali::Ui::Visual::ResourceStatus GetStatus(Dali::Ui::LottieAnimationView animation)
{
  return animation.GetLoadingStatus();
}
```

## Tune Loading and Rasterization

Use `Dali::Ui::LottieAnimationView::SetPlaceholderUrl` to show an image while a Lottie resource is loading.

```cpp
Dali::Ui::LottieAnimationView CreateLoadingPreview()
{
  return Dali::Ui::LottieAnimationView::New("animations/main.json")
    .SetPlaceholderUrl("images/loading-preview.png")
    .SetLoopCount(-1);
}
```

Use `Dali::Ui::LottieAnimationView::SetReleasePolicy` to control when cached resources are released.

```cpp
void KeepAnimationCached(Dali::Ui::LottieAnimationView animation)
{
  animation.SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::NEVER);
}

void UseDefaultDetachRelease(Dali::Ui::LottieAnimationView animation)
{
  animation.SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DETACHED);
}
```

Use `Dali::Ui::LottieAnimationView::SetSynchronousLoading` only when the app intentionally wants loading on the event thread.

```cpp
Dali::Ui::LottieAnimationView CreateImmediatePreview(const Dali::String& url)
{
  return Dali::Ui::LottieAnimationView::New(url)
    .SetSynchronousLoading(true)
    .SetDesiredWidth(128)
    .SetDesiredHeight(128);
}
```

Use rasterization options to trade memory, sharpness, and redraw cost. `Dali::Ui::LottieAnimationView::SetEnableFrameCache` caches rasterized frames for repeated playback. `Dali::Ui::LottieAnimationView::SetRenderScale` changes the rasterization scale relative to the visual size. `Dali::Ui::LottieAnimationView::SetRedrawOnScaleDown` and `Dali::Ui::LottieAnimationView::SetRedrawOnScaleUp` control redraw behavior during scaling.

```cpp
void TuneForRepeatedLooping(Dali::Ui::LottieAnimationView animation)
{
  animation
    .SetEnableFrameCache(true)
    .SetRenderScale(1.0f)
    .SetRedrawOnScaleDown(true)
    .SetRedrawOnScaleUp(true);
}
```

For low-frame-rate Lottie files, `Dali::Ui::LottieAnimationView::SetNotifyAfterRasterization` controls the notify-after-rasterization option.

```cpp
void EnableRasterizationNotification(Dali::Ui::LottieAnimationView animation)
{
  animation.SetNotifyAfterRasterization(true);
}
```

Read the active loading and rasterization settings with `Dali::Ui::LottieAnimationView::GetPlaceholderUrl`, `Dali::Ui::LottieAnimationView::GetReleasePolicy`, `Dali::Ui::LottieAnimationView::IsSynchronousLoading`, `Dali::Ui::LottieAnimationView::IsFrameCacheEnabled`, `Dali::Ui::LottieAnimationView::GetRenderScale`, `Dali::Ui::LottieAnimationView::IsRedrawOnScaleDown`, `Dali::Ui::LottieAnimationView::IsRedrawOnScaleUp`, and `Dali::Ui::LottieAnimationView::IsNotifyAfterRasterizationEnabled`.

```cpp
bool UsesFrameCache(Dali::Ui::LottieAnimationView animation)
{
  return animation.IsFrameCacheEnabled();
}
```

## Adjust Appearance

Use `Dali::Ui::LottieAnimationView::SetImageColor` to apply an RGBA color multiplier to the rendered animation. White leaves the original colors unchanged.

```cpp
void TintErrorAnimation(Dali::Ui::LottieAnimationView animation)
{
  animation.SetImageColor(Dali::Ui::UiColor(1.0f, 0.231f, 0.188f, 1.0f));
}

Dali::Ui::UiColor CurrentTint(Dali::Ui::LottieAnimationView animation)
{
  return animation.GetImageColor();
}
```

Use `Dali::Ui::LottieAnimationView::SetPixelArea` to display a normalized sub-region of the animation texture.

```cpp
void ShowTopLeftQuarter(Dali::Ui::LottieAnimationView animation)
{
  animation.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.5f, 0.5f));
}

Dali::Vector4 CurrentPixelArea(Dali::Ui::LottieAnimationView animation)
{
  return animation.GetPixelArea();
}
```

Use `Dali::Ui::LottieAnimationView::SetPreMultipliedAlpha` when the rendered frames should use pre-multiplied alpha.

```cpp
void ConfigureAlpha(Dali::Ui::LottieAnimationView animation)
{
  animation.SetPreMultipliedAlpha(true);
}

bool UsesPreMultipliedAlpha(Dali::Ui::LottieAnimationView animation)
{
  return animation.IsPreMultipliedAlpha();
}
```

For per-frame or per-layer animation changes, use `Dali::Ui::LottieAnimationView::SetDynamicProperty` with `Dali::Ui::LottieAnimation::DynamicPropertyInfo`. Call it after the view has a resource URL so the visual can be created. The callback ownership is transferred to the visual, and the callback must not call DALi APIs because it runs on a worker thread.

```cpp
void SetDynamicFillCallback(
  Dali::Ui::LottieAnimationView animation,
  Dali::CallbackBase* callback)
{
  Dali::Ui::LottieAnimation::DynamicPropertyInfo info;
  info.id       = 1;
  info.keyPath  = "**";
  info.property = Dali::Ui::LottieAnimation::VectorProperty::FILL_COLOR;
  info.callback = callback;

  animation.SetDynamicProperty(info);
}
```

## Use Lottie Properties

Prefer typed setters and getters for application code. `Dali::Ui::LottieAnimationView::Property` owns the public property constants for this view and maps them to `Dali::Ui::LottieAnimationViewPropertyIndex`.

The source URL property is `Dali::Ui::LottieAnimationView::Property::IMAGE`, backed by `Dali::Ui::LottieAnimationViewPropertyIndex::IMAGE`. The typed API is `Dali::Ui::LottieAnimationView::SetResourceUrl` and `Dali::Ui::LottieAnimationView::GetResourceUrl`.

```cpp
Dali::String CurrentUrl(Dali::Ui::LottieAnimationView animation)
{
  return animation.GetResourceUrl();
}
```

Playback properties include `Dali::Ui::LottieAnimationView::Property::LOOP_COUNT`, `Dali::Ui::LottieAnimationView::Property::LOOPING_MODE`, `Dali::Ui::LottieAnimationView::Property::STOP_BEHAVIOR`, and `Dali::Ui::LottieAnimationView::Property::FRAME_SPEED_FACTOR`.

```cpp
void ConfigurePlaybackDefaults(Dali::Ui::LottieAnimationView animation)
{
  animation
    .SetLoopCount(-1)
    .SetLoopingMode(Dali::Ui::LottieAnimation::LoopingMode::RESTART)
    .SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::CURRENT_FRAME)
    .SetFrameSpeedFactor(1.0f);
}
```

Loading and resource properties include `Dali::Ui::LottieAnimationView::Property::DESIRED_WIDTH`, `Dali::Ui::LottieAnimationView::Property::DESIRED_HEIGHT`, `Dali::Ui::LottieAnimationView::Property::RELEASE_POLICY`, `Dali::Ui::LottieAnimationView::Property::SYNCHRONOUS_LOADING`, `Dali::Ui::LottieAnimationView::Property::PLACEHOLDER_IMAGE`, and `Dali::Ui::LottieAnimationView::Property::PRE_MULTIPLIED_ALPHA`.

```cpp
void ConfigureLoadingDefaults(Dali::Ui::LottieAnimationView animation)
{
  animation
    .SetDesiredWidth(256)
    .SetDesiredHeight(256)
    .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DETACHED)
    .SetSynchronousLoading(false)
    .SetPreMultipliedAlpha(false);
}
```

Rasterization and appearance properties include `Dali::Ui::LottieAnimationView::Property::IMAGE_COLOR`, `Dali::Ui::LottieAnimationView::Property::PIXEL_AREA`, `Dali::Ui::LottieAnimationView::Property::REDRAW_IN_SCALING_DOWN`, `Dali::Ui::LottieAnimationView::Property::REDRAW_IN_SCALING_UP`, `Dali::Ui::LottieAnimationView::Property::ENABLE_FRAME_CACHE`, `Dali::Ui::LottieAnimationView::Property::NOTIFY_AFTER_RASTERIZATION`, and `Dali::Ui::LottieAnimationView::Property::RENDER_SCALE`.

```cpp
void ConfigureRenderDefaults(Dali::Ui::LottieAnimationView animation)
{
  animation
    .SetImageColor(Dali::Ui::UiColor(0xFFFFFF))
    .SetPixelArea(Dali::Vector4(0.0f, 0.0f, 1.0f, 1.0f))
    .SetRedrawOnScaleDown(true)
    .SetRedrawOnScaleUp(true)
    .SetEnableFrameCache(false)
    .SetNotifyAfterRasterization(false)
    .SetRenderScale(1.0f);
}
```
