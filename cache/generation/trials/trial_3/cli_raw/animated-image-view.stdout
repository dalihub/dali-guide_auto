---
title: Animated Image View
sidebar_label: Animated Image View
category: views-components
---

# Animated Image View

`Dali::Ui::AnimatedImageView` displays and controls animated image resources in a dali-ui view tree.

## Table of contents

- [Create an animated image view](#create-an-animated-image-view)
- [Choose the image source](#choose-the-image-source)
- [Control playback and frames](#control-playback-and-frames)
- [Configure loops, speed, and stop behavior](#configure-loops-speed-and-stop-behavior)
- [Tune loading and frame buffering](#tune-loading-and-frame-buffering)
- [Apply fitting, color, pixel area, and masks](#apply-fitting-color-pixel-area-and-masks)
- [React to loading and animation signals](#react-to-loading-and-animation-signals)
- [Property constants](#property-constants)

## Create an animated image view

Use `Dali::Ui::AnimatedImageView::New` to create the app-facing view object. The returned `Dali::Ui::AnimatedImageView` is also a `Dali::Ui::View`, so it can be placed in a dali-ui view tree without using raw `Dali::Actor` idioms.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

Dali::Ui::AnimatedImageView loadingView =
  Dali::Ui::AnimatedImageView::New("images/loading.gif")
    .SetLoopCount(-1)
    .SetDesiredWidth(256)
    .SetDesiredHeight(256)
    .SetImageLoadWithViewSize(true);

Dali::Ui::View appFacingView = loadingView;
```

`Dali::Ui::AnimatedImageView::New` also accepts no URL. This is useful when the source is selected later with `SetResourceUrl` or `SetResourceUrls`.

```cpp
Dali::Ui::AnimatedImageView preview = Dali::Ui::AnimatedImageView::New();

preview
  .SetDesiredWidth(320)
  .SetDesiredHeight(240)
  .SetLoopCount(1);
```

## Choose the image source

For a single animated resource such as GIF or animated WebP, call `SetResourceUrl`. The current source can be read back with `GetResourceUrl`.

```cpp
void ShowSingleAnimation(Dali::Ui::AnimatedImageView view,
                         const Dali::String& url)
{
  view
    .SetResourceUrl(url)
    .SetResourceUrls(Dali::Vector<Dali::String>{})
    .Play();

  Dali::String currentUrl = view.GetResourceUrl();
}
```

For frame-by-frame animation from separate image files, use `SetResourceUrls`. The frame sequence is stored as `Dali::Vector<Dali::String>` and can be inspected with `GetResourceUrls`.

```cpp
Dali::Vector<Dali::String> MakeFrameUrls()
{
  Dali::Vector<Dali::String> urls;
  urls.PushBack(Dali::String("images/walk-001.png"));
  urls.PushBack(Dali::String("images/walk-002.png"));
  urls.PushBack(Dali::String("images/walk-003.png"));
  urls.PushBack(Dali::String("images/walk-004.png"));
  return urls;
}

void ShowFrameSequence(Dali::Ui::AnimatedImageView view)
{
  view
    .SetResourceUrls(MakeFrameUrls())
    .SetFrameDelay(80)
    .SetBatchSize(4)
    .SetCacheSize(8)
    .Play();

  const Dali::Vector<Dali::String>& urls = view.GetResourceUrls();
}
```

`SetResourceUrls` overrides the single URL previously supplied through `SetResourceUrl`. When switching back to a single animated file, clear the URL array before calling `SetResourceUrl`.

## Control playback and frames

Use `Play`, `Pause`, and `Stop` for playback. Use `JumpToFrame` when the UI needs to seek to a specific frame.

```cpp
void PlayPreview(Dali::Ui::AnimatedImageView view)
{
  view.Play();
}

void PausePreview(Dali::Ui::AnimatedImageView view)
{
  view.Pause();
}

void StopPreview(Dali::Ui::AnimatedImageView view)
{
  view.Stop();
}

void SeekPreview(Dali::Ui::AnimatedImageView view, int frame)
{
  view.JumpToFrame(frame);
}
```

Playback state and frame position are available through `GetPlayState`, `GetCurrentFrame`, and `GetTotalFrame`.

```cpp
bool IsCurrentlyPlaying(Dali::Ui::AnimatedImageView view)
{
  return view.GetPlayState() == Dali::Ui::AnimatedImage::PlayState::PLAYING;
}

int GetRemainingFrames(Dali::Ui::AnimatedImageView view)
{
  return view.GetTotalFrame() - view.GetCurrentFrame();
}
```

## Configure loops, speed, and stop behavior

`SetLoopCount` controls how many times the animation repeats. A value of `-1` means infinite looping, `0` means it will not play, and a positive value sets an exact loop count.

```cpp
Dali::Ui::AnimatedImageView spinner =
  Dali::Ui::AnimatedImageView::New("images/spinner.webp")
    .SetLoopCount(-1)
    .SetFrameSpeedFactor(1.0f)
    .Play();

int loopCount = spinner.GetLoopCount();
float speed = spinner.GetFrameSpeedFactor();
```

Use `SetFrameSpeedFactor` to scale playback speed and `SetFrameDelay` to override the per-frame delay in milliseconds.

```cpp
void UseFastPreviewTiming(Dali::Ui::AnimatedImageView view)
{
  view
    .SetFrameSpeedFactor(2.0f)
    .SetFrameDelay(100);

  float factor = view.GetFrameSpeedFactor();
  int delayMs = view.GetFrameDelay();
}
```

`SetStopBehavior` controls which frame is shown after `Stop`. The value is read back with `GetStopBehavior`.

```cpp
void StopOnLastFrame(Dali::Ui::AnimatedImageView view)
{
  view
    .SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::LAST_FRAME)
    .Stop();

  Dali::Ui::AnimatedImage::StopBehavior behavior = view.GetStopBehavior();
}
```

## Tune loading and frame buffering

`SetBatchSize` sets how many frames are prepared in a batch. `SetCacheSize` sets how many frames are kept cached.

```cpp
Dali::Ui::AnimatedImageView sequence =
  Dali::Ui::AnimatedImageView::New()
    .SetResourceUrls(MakeFrameUrls())
    .SetBatchSize(4)
    .SetCacheSize(10)
    .SetFrameDelay(120);

int batchSize = sequence.GetBatchSize();
int cacheSize = sequence.GetCacheSize();
```

Use `SetDesiredWidth` and `SetDesiredHeight` as loader size hints. `SetImageLoadWithViewSize` enables loading based on the current view size.

```cpp
void ConfigureLoaderSize(Dali::Ui::AnimatedImageView view)
{
  view
    .SetDesiredWidth(480)
    .SetDesiredHeight(320)
    .SetImageLoadWithViewSize(true);

  int width = view.GetDesiredWidth();
  int height = view.GetDesiredHeight();
  bool usesViewSize = view.IsImageLoadWithViewSizeEnabled();
}
```

`SetLoadPolicy`, `SetReleasePolicy`, and `SetSynchronousLoading` control when resources are loaded, when cached resources are released, and whether loading is synchronous.

```cpp
void ConfigureLoadingPolicy(Dali::Ui::AnimatedImageView view,
                            Dali::Ui::Image::LoadPolicy loadPolicy,
                            Dali::Ui::Image::ReleasePolicy releasePolicy)
{
  view
    .SetLoadPolicy(loadPolicy)
    .SetReleasePolicy(releasePolicy)
    .SetSynchronousLoading(false);

  Dali::Ui::Image::LoadPolicy currentLoadPolicy = view.GetLoadPolicy();
  Dali::Ui::Image::ReleasePolicy currentReleasePolicy = view.GetReleasePolicy();
  bool synchronous = view.IsSynchronousLoading();
}
```

## Apply fitting, color, pixel area, and masks

Use `SetFittingMode` to control how the animated image fits inside the view bounds, and `SetSamplingMode` to control scaling quality.

```cpp
void ConfigureImageScaling(Dali::Ui::AnimatedImageView view,
                           Dali::Ui::Image::FittingMode fittingMode,
                           Dali::Ui::Image::SamplingMode samplingMode)
{
  view
    .SetFittingMode(fittingMode)
    .SetSamplingMode(samplingMode);

  Dali::Ui::Image::FittingMode currentFitting = view.GetFittingMode();
  Dali::Ui::Image::SamplingMode currentSampling = view.GetSamplingMode();
}
```

`SetImageColor` applies a color to the image, and `SetPreMultipliedAlpha` configures alpha handling.

```cpp
void TintAnimatedImage(Dali::Ui::AnimatedImageView view)
{
  view
    .SetImageColor(Dali::Ui::UiColor(0xFFFFFFFF))
    .SetPreMultipliedAlpha(true);

  Dali::Ui::UiColor color = view.GetImageColor();
  bool preMultiplied = view.IsPreMultipliedAlpha();
}
```

Use `SetPixelArea` to show a normalized sub-region of the image. The value is `(x, y, width, height)`.

```cpp
void ShowCenterRegion(Dali::Ui::AnimatedImageView view)
{
  view.SetPixelArea(Dali::Vector4(0.25f, 0.25f, 0.5f, 0.5f));

  Dali::Vector4 area = view.GetPixelArea();
}
```

For alpha masking, set a mask image with `SetAlphaMaskUrl`, choose whether to crop to the mask bounds with `SetCropToMask`, and choose masking timing with `SetMaskingMode`.

```cpp
void ApplyAlphaMask(Dali::Ui::AnimatedImageView view,
                    const Dali::String& maskUrl,
                    Dali::Ui::Image::MaskingType maskingMode)
{
  view
    .SetAlphaMaskUrl(maskUrl)
    .SetCropToMask(true)
    .SetMaskingMode(maskingMode);

  Dali::String currentMask = view.GetAlphaMaskUrl();
  bool cropToMask = view.IsCropToMask();
  Dali::Ui::Image::MaskingType currentMode = view.GetMaskingMode();
}
```

A fallback image can be supplied while the animated resource is loading.

```cpp
void UseFallbackImage(Dali::Ui::AnimatedImageView view,
                      const Dali::String& fallbackUrl,
                      const Dali::String& animationUrl)
{
  view
    .SetPlaceholderUrl(fallbackUrl)
    .SetResourceUrl(animationUrl);

  Dali::String fallback = view.GetPlaceholderUrl();
}
```

## React to loading and animation signals

`ResourceReadySignal` is emitted when the image resource is ready to display. The callback receives the owning `Dali::Ui::View`.

```cpp
class AnimatedImageController
{
public:
  void SetView(Dali::Ui::AnimatedImageView view)
  {
    mView = view;
    mView.ResourceReadySignal().Connect(this, &AnimatedImageController::OnResourceReady);
  }

private:
  void OnResourceReady(Dali::Ui::View view)
  {
    Dali::Ui::Visual::ResourceStatus status = mView.GetLoadingStatus();
    int totalFrames = mView.GetTotalFrame();

    if(status == Dali::Ui::Visual::ResourceStatus::READY && totalFrames > 0)
    {
      mView.Play();
    }
  }

  Dali::Ui::AnimatedImageView mView;
};
```

`AnimationFinishedSignal` is emitted when playback completes its configured loops. It also passes the owning `Dali::Ui::View`.

```cpp
class LoopOnceController
{
public:
  void SetView(Dali::Ui::AnimatedImageView view)
  {
    mView = view;
    mView
      .SetLoopCount(1)
      .SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::LAST_FRAME);

    mView.AnimationFinishedSignal().Connect(this, &LoopOnceController::OnAnimationFinished);
  }

  void Start()
  {
    mView.Play();
  }

private:
  void OnAnimationFinished(Dali::Ui::View view)
  {
    mView.Stop();
  }

  Dali::Ui::AnimatedImageView mView;
};
```

If a generic `BaseHandle` is passed through shared code, use `Dali::Ui::AnimatedImageView::DownCast` before accessing animated-image-specific APIs.

```cpp
void ConnectIfAnimatedImage(Dali::BaseHandle handle,
                            AnimatedImageController& controller)
{
  Dali::Ui::AnimatedImageView view =
    Dali::Ui::AnimatedImageView::DownCast(handle);

  if(view)
  {
    controller.SetView(view);
  }
}
```

## Property constants

Prefer typed setters such as `SetResourceUrl`, `SetLoopCount`, `SetFrameDelay`, `SetFittingMode`, and `SetAlphaMaskUrl` in application code. `Dali::Ui::AnimatedImageView::Property` owns the property constants for integrations that need property indices.

The property owner is `Dali::Ui::AnimatedImageView::Property`, including:

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

`Dali::Ui::AnimatedImageViewPropertyIndex` provides the backing property-index values used by `Dali::Ui::AnimatedImageView::Property`. For application code, use the `Dali::Ui::AnimatedImageView::Property` owner name when referring to Animated Image View properties.
