---
title: Lottie Animation View
sidebar_label: Lottie Animation View
category: views-components
---

# Lottie Animation View

`Dali::Ui::LottieAnimationView` displays and controls JSON-based Lottie vector animations in a dali-ui view tree.

## Table of Contents

- [Create a Lottie View](#create-a-lottie-view)
- [Control Playback](#control-playback)
- [Looping and Frame Ranges](#looping-and-frame-ranges)
- [Loading, Placeholders, and Resource Signals](#loading-placeholders-and-resource-signals)
- [Rendering Quality and Cache Tuning](#rendering-quality-and-cache-tuning)
- [Color and Visible Region](#color-and-visible-region)
- [Inspect Content and Markers](#inspect-content-and-markers)
- [Property Constants](#property-constants)

## Create a Lottie View

Create a `Dali::Ui::LottieAnimationView` with `Dali::Ui::LottieAnimationView::New()`. The URL passed to `New()` is the Lottie JSON resource used by the view.

```cpp
Dali::Ui::LottieAnimationView lottie =
  Dali::Ui::LottieAnimationView::New("animations/loading.json")
    .SetDesiredWidth(256)
    .SetDesiredHeight(256)
    .SetLoopCount(-1)
    .SetFrameSpeedFactor(1.0f);

lottie.Play();
```

Use `Dali::Ui::LottieAnimationView::SetResourceUrl()` when the same view should load another Lottie file.

```cpp
Dali::Ui::LottieAnimationView lottie = Dali::Ui::LottieAnimationView::New();

lottie
  .SetResourceUrl("animations/success.json")
  .SetDesiredWidth(320)
  .SetDesiredHeight(320)
  .SetLoopCount(1)
  .Play();

Dali::String currentUrl = lottie.GetResourceUrl();
int desiredWidth = lottie.GetDesiredWidth();
int desiredHeight = lottie.GetDesiredHeight();
```

`Dali::Ui::LottieAnimationView` is a `Dali::Ui::View`, so app code should keep it as part of the dali-ui view model rather than treating it as a raw actor.

```cpp
Dali::Ui::View contentView =
  Dali::Ui::LottieAnimationView::New("animations/intro.json")
    .SetLoopCount(1)
    .SetDepthIndex(2)
    .Play();
```

## Control Playback

Use `Dali::Ui::LottieAnimationView::Play()`, `Dali::Ui::LottieAnimationView::Pause()`, and `Dali::Ui::LottieAnimationView::Stop()` for direct playback control. Query `Dali::Ui::LottieAnimationView::GetPlayState()`, `Dali::Ui::LottieAnimationView::GetCurrentFrame()`, and `Dali::Ui::LottieAnimationView::GetTotalFrame()` when updating UI state.

```cpp
void TogglePlayback(Dali::Ui::LottieAnimationView lottie)
{
  if(lottie.GetPlayState() == Dali::Ui::AnimatedImage::PlayState::PLAYING)
  {
    lottie.Pause();
  }
  else
  {
    lottie.Play();
  }
}

void RestartFromBeginning(Dali::Ui::LottieAnimationView lottie)
{
  lottie
    .Stop()
    .JumpToFrame(0)
    .Play();
}
```

Use `Dali::Ui::LottieAnimationView::JumpToFrame()` for timeline scrubbing or for displaying a specific frame.

```cpp
void ShowProgressFrame(Dali::Ui::LottieAnimationView lottie, int frame)
{
  int totalFrame = lottie.GetTotalFrame();

  if(totalFrame > 0 && frame >= 0 && frame < totalFrame)
  {
    lottie.JumpToFrame(frame);
  }
}
```

`Dali::Ui::LottieAnimationView::AnimationFinishedSignal()` is emitted when playback finishes all configured loops.

```cpp
class AnimationController : public Dali::ConnectionTracker
{
public:
  void Configure(Dali::Ui::LottieAnimationView lottie)
  {
    mLottie = lottie;
    mLottie.AnimationFinishedSignal().Connect(this, &AnimationController::OnAnimationFinished);
  }

private:
  void OnAnimationFinished(Dali::Ui::View view)
  {
    Dali::Ui::LottieAnimationView finishedView =
      Dali::Ui::LottieAnimationView::DownCast(view);

    finishedView.JumpToFrame(0);
  }

  Dali::Ui::LottieAnimationView mLottie;
};
```

## Looping and Frame Ranges

Use `Dali::Ui::LottieAnimationView::SetLoopCount()` to choose how often playback repeats. `-1` means infinite looping, `0` prevents playback, and a positive value plays that many loops.

```cpp
Dali::Ui::LottieAnimationView spinner =
  Dali::Ui::LottieAnimationView::New("animations/spinner.json")
    .SetLoopCount(-1)
    .Play();

Dali::Ui::LottieAnimationView confirmation =
  Dali::Ui::LottieAnimationView::New("animations/check.json")
    .SetLoopCount(1)
    .Play();

int confirmationLoops = confirmation.GetLoopCount();
```

Use `Dali::Ui::LottieAnimationView::SetLoopingMode()` to select the loop direction behavior. `Dali::Ui::LottieAnimation::LoopingMode::RESTART` restarts at the beginning for each loop, while `Dali::Ui::LottieAnimation::LoopingMode::AUTO_REVERSE` reverses direction at the end.

```cpp
Dali::Ui::LottieAnimationView pulse =
  Dali::Ui::LottieAnimationView::New("animations/pulse.json")
    .SetLoopCount(-1)
    .SetLoopingMode(Dali::Ui::LottieAnimation::LoopingMode::AUTO_REVERSE)
    .SetFrameSpeedFactor(0.75f)
    .Play();

Dali::Ui::LottieAnimation::LoopingMode mode = pulse.GetLoopingMode();
float speed = pulse.GetFrameSpeedFactor();
```

Use `Dali::Ui::LottieAnimationView::SetMinMaxFrame()` to limit playback to a numeric frame interval. Use `Dali::Ui::LottieAnimationView::SetMinMaxFrameByMarker()` when the Lottie file contains marker names.

```cpp
void PlayFirstHalf(Dali::Ui::LottieAnimationView lottie)
{
  int totalFrame = lottie.GetTotalFrame();

  if(totalFrame > 1)
  {
    lottie
      .SetMinMaxFrame(0, totalFrame / 2)
      .JumpToFrame(0)
      .Play();
  }
}

void PlayNamedSegment(Dali::Ui::LottieAnimationView lottie)
{
  lottie
    .SetMinMaxFrameByMarker("intro", "complete")
    .SetLoopCount(1)
    .Play();
}
```

`Dali::Ui::LottieAnimationView::SetStopBehavior()` controls which frame remains visible after `Dali::Ui::LottieAnimationView::Stop()`.

```cpp
Dali::Ui::LottieAnimationView lottie =
  Dali::Ui::LottieAnimationView::New("animations/result.json")
    .SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::LAST_FRAME)
    .SetLoopCount(1);

lottie.Play();

Dali::Ui::AnimatedImage::StopBehavior behavior = lottie.GetStopBehavior();
```

## Loading, Placeholders, and Resource Signals

Use `Dali::Ui::LottieAnimationView::SetPlaceholderUrl()` to show an image while the Lottie resource is loading. `Dali::Ui::LottieAnimationView::ResourceReadySignal()` lets the app react when the visual resource finishes loading or reports a later readiness event.

```cpp
class LottieLoader : public Dali::ConnectionTracker
{
public:
  Dali::Ui::LottieAnimationView Create()
  {
    mLottie =
      Dali::Ui::LottieAnimationView::New()
        .SetPlaceholderUrl("images/lottie-placeholder.png")
        .SetResourceUrl("animations/onboarding.json")
        .SetLoopCount(1);

    mLottie.ResourceReadySignal().Connect(this, &LottieLoader::OnResourceReady);
    return mLottie;
  }

private:
  void OnResourceReady(Dali::Ui::View view)
  {
    Dali::Ui::LottieAnimationView readyView =
      Dali::Ui::LottieAnimationView::DownCast(view);

    if(readyView.GetLoadingStatus() == Dali::Ui::Visual::ResourceStatus::READY)
    {
      readyView.Play();
    }
  }

  Dali::Ui::LottieAnimationView mLottie;
};
```

Use `Dali::Ui::LottieAnimationView::GetLoadingStatus()` to check the current visual resource status, and `Dali::Ui::LottieAnimationView::GetPlaceholderUrl()` to inspect the configured placeholder.

```cpp
Dali::Ui::LottieAnimationView lottie =
  Dali::Ui::LottieAnimationView::New("animations/download.json")
    .SetPlaceholderUrl("images/download-placeholder.png");

Dali::Ui::Visual::ResourceStatus status = lottie.GetLoadingStatus();
Dali::String placeholderUrl = lottie.GetPlaceholderUrl();

if(status == Dali::Ui::Visual::ResourceStatus::READY)
{
  lottie.Play();
}
```

`Dali::Ui::LottieAnimationView::SetSynchronousLoading()` requests synchronous loading on the event thread. Use it only for small resources where blocking is acceptable.

```cpp
Dali::Ui::LottieAnimationView icon =
  Dali::Ui::LottieAnimationView::New("animations/tiny-icon.json")
    .SetSynchronousLoading(true)
    .SetLoopCount(1);

bool synchronous = icon.IsSynchronousLoading();
```

Use `Dali::Ui::LottieAnimationView::SetReleasePolicy()` to control when the visual's texture resources may be released.

```cpp
Dali::Ui::LottieAnimationView lottie =
  Dali::Ui::LottieAnimationView::New("animations/persistent.json")
    .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::NEVER);

Dali::Ui::Image::ReleasePolicy policy = lottie.GetReleasePolicy();
```

## Rendering Quality and Cache Tuning

`Dali::Ui::LottieAnimationView` rasterizes vector animation content for display. Use desired size and render scale together when the animation needs a predictable rasterization size.

```cpp
Dali::Ui::LottieAnimationView lottie =
  Dali::Ui::LottieAnimationView::New("animations/hero.json")
    .SetDesiredWidth(480)
    .SetDesiredHeight(480)
    .SetRenderScale(2.0f);

float renderScale = lottie.GetRenderScale();
```

Use `Dali::Ui::LottieAnimationView::SetEnableFrameCache()` for animations that loop frequently and can afford higher memory usage. Use the redraw controls when scaling changes should force fresh rasterization.

```cpp
Dali::Ui::LottieAnimationView loopingBadge =
  Dali::Ui::LottieAnimationView::New("animations/badge.json")
    .SetLoopCount(-1)
    .SetEnableFrameCache(true)
    .SetRedrawOnScaleUp(true)
    .SetRedrawOnScaleDown(true)
    .Play();

bool cacheEnabled = loopingBadge.IsFrameCacheEnabled();
bool redrawUp = loopingBadge.IsRedrawOnScaleUp();
bool redrawDown = loopingBadge.IsRedrawOnScaleDown();
```

`Dali::Ui::LottieAnimationView::SetNotifyAfterRasterization()` controls whether `Dali::Ui::LottieAnimationView::ResourceReadySignal()` is notified after each rasterization completes.

```cpp
Dali::Ui::LottieAnimationView lottie =
  Dali::Ui::LottieAnimationView::New("animations/low-fps.json")
    .SetNotifyAfterRasterization(true);

bool notify = lottie.IsNotifyAfterRasterizationEnabled();
```

Use `Dali::Ui::LottieAnimationView::SetPreMultipliedAlpha()` when the rendered frames must use pre-multiplied alpha.

```cpp
Dali::Ui::LottieAnimationView lottie =
  Dali::Ui::LottieAnimationView::New("animations/overlay.json")
    .SetPreMultipliedAlpha(true);

bool preMultiplied = lottie.IsPreMultipliedAlpha();
```

## Color and Visible Region

Use `Dali::Ui::LottieAnimationView::SetImageColor()` to apply a color multiplier to the full rendered animation.

```cpp
Dali::Ui::LottieAnimationView warning =
  Dali::Ui::LottieAnimationView::New("animations/status.json")
    .SetImageColor(Dali::Ui::UiColor(0xFF5555FF))
    .SetLoopCount(-1)
    .Play();

Dali::Ui::UiColor tint = warning.GetImageColor();
```

Use `Dali::Ui::LottieAnimationView::SetPixelArea()` to display a normalized sub-region of the animation. The area is `(x, y, width, height)` in normalized coordinates.

```cpp
Dali::Ui::LottieAnimationView cropped =
  Dali::Ui::LottieAnimationView::New("animations/sheet.json")
    .SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.5f, 0.5f));

Dali::Vector4 pixelArea = cropped.GetPixelArea();
```

For per-layer animation changes, use `Dali::Ui::LottieAnimationView::SetDynamicProperty()` with a `Dali::Ui::LottieAnimation::DynamicPropertyInfo`. The callback is owned by the visual after it is set, and it is invoked from a worker thread, so it should not call DALi APIs.

```cpp
Dali::Ui::LottieAnimation::DynamicPropertyInfo info;
info.id = 1;
info.keyPath = "Layer 1.Ellipse 1.Fill 1";
info.property = Dali::Ui::LottieAnimation::VectorProperty::FILL_COLOR;
info.callback = CreateColorCallback();

Dali::Ui::LottieAnimationView lottie =
  Dali::Ui::LottieAnimationView::New("animations/shape.json")
    .SetDynamicProperty(info);
```

## Inspect Content and Markers

Use `Dali::Ui::LottieAnimationView::GetContentInfo()` to read layer information from the Lottie file. The returned `Dali::Property::Map` uses layer names as keys and two-element integer arrays, `[startFrame, endFrame]`, as values.

```cpp
void CheckLayerInfoAvailable(Dali::Ui::LottieAnimationView lottie)
{
  Dali::Property::Map contentInfo = lottie.GetContentInfo();
  bool hasLayerInfo = contentInfo["Layer 1"].GetType() != Dali::Property::NONE;
}
```

Use `Dali::Ui::LottieAnimationView::GetMarkerInfo()` before playing marker-based segments. The returned `Dali::Property::Map` uses marker names as keys and two-element integer arrays, `[startFrame, endFrame]`, as values.

```cpp
void PlayMarkerIfAvailable(Dali::Ui::LottieAnimationView lottie)
{
  Dali::Property::Map markerInfo = lottie.GetMarkerInfo();

  if(markerInfo["intro"].GetType() != Dali::Property::NONE)
  {
    lottie
      .SetMinMaxFrameByMarker("intro")
      .SetLoopCount(1)
      .Play();
  }
}
```

## Property Constants

Prefer typed setters such as `Dali::Ui::LottieAnimationView::SetLoopCount()`, `Dali::Ui::LottieAnimationView::SetRenderScale()`, and `Dali::Ui::LottieAnimationView::SetPlaceholderUrl()` in application code. The property constants are available through `Dali::Ui::LottieAnimationView::Property` for code that needs property indices, and they map to the shared `Dali::Ui::LottieAnimationViewPropertyIndex` values.

| Property constant | Purpose |
| --- | --- |
| `Dali::Ui::LottieAnimationView::Property::IMAGE` | Lottie JSON resource URL |
| `Dali::Ui::LottieAnimationView::Property::LOOP_COUNT` | Number of loops, with `-1` for infinite looping |
| `Dali::Ui::LottieAnimationView::Property::LOOPING_MODE` | `Dali::Ui::LottieAnimation::LoopingMode` value |
| `Dali::Ui::LottieAnimationView::Property::STOP_BEHAVIOR` | `Dali::Ui::AnimatedImage::StopBehavior` value |
| `Dali::Ui::LottieAnimationView::Property::FRAME_SPEED_FACTOR` | Playback speed multiplier |
| `Dali::Ui::LottieAnimationView::Property::IMAGE_COLOR` | Full-animation color multiplier |
| `Dali::Ui::LottieAnimationView::Property::DESIRED_WIDTH` | Desired rasterization width hint in pixels |
| `Dali::Ui::LottieAnimationView::Property::DESIRED_HEIGHT` | Desired rasterization height hint in pixels |
| `Dali::Ui::LottieAnimationView::Property::RELEASE_POLICY` | `Dali::Ui::Image::ReleasePolicy` value |
| `Dali::Ui::LottieAnimationView::Property::SYNCHRONOUS_LOADING` | Whether loading is synchronous |
| `Dali::Ui::LottieAnimationView::Property::PRE_MULTIPLIED_ALPHA` | Whether pre-multiplied alpha is used |
| `Dali::Ui::LottieAnimationView::Property::REDRAW_IN_SCALING_DOWN` | Whether to redraw while scaling down |
| `Dali::Ui::LottieAnimationView::Property::REDRAW_IN_SCALING_UP` | Whether to redraw while scaling up |
| `Dali::Ui::LottieAnimationView::Property::ENABLE_FRAME_CACHE` | Whether rasterized frames are cached |
| `Dali::Ui::LottieAnimationView::Property::NOTIFY_AFTER_RASTERIZATION` | Whether rasterization emits readiness notifications |
| `Dali::Ui::LottieAnimationView::Property::RENDER_SCALE` | Rasterization scale multiplier |
| `Dali::Ui::LottieAnimationView::Property::PLACEHOLDER_IMAGE` | Placeholder image URL |
| `Dali::Ui::LottieAnimationView::Property::PIXEL_AREA` | Normalized visible sub-region |
