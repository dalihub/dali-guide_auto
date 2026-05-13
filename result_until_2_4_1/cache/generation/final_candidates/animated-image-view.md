---
title: Animated Image View
sidebar_label: Animated Image View
category: views-components
---

# Animated Image View

`Dali::Ui::AnimatedImageView` displays animated image resources such as GIF or WebP files in a dali-ui application, and provides view-level playback, loading, masking, and frame controls.

## Table of Contents

- [Create an Animated Image View](#create-an-animated-image-view)
- [Choose the Image Source](#choose-the-image-source)
- [Control Playback and Frames](#control-playback-and-frames)
- [Tune Loading and Rendering](#tune-loading-and-rendering)
- [Use Masks, Placeholders, and Pixel Areas](#use-masks-placeholders-and-pixel-areas)
- [Handle Resource and Animation Signals](#handle-resource-and-animation-signals)
- [Property Constants](#property-constants)

## Create an Animated Image View

Use `Dali::Ui::AnimatedImageView::New` to create the view. The optional URL parameter sets the initial animated image resource. The object is a `Dali::Ui::View`, so application code should keep it in the dali-ui view tree rather than treating it as a raw actor.

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

`SetLoopCount(-1)` configures infinite playback. `SetFrameSpeedFactor(1.0f)` keeps the file's normal playback speed. `SetBatchSize` sets the number of frames to pre-load in each batch, and `SetCacheSize` sets the number of frames to keep in cache.

## Choose the Image Source

`Dali::Ui::AnimatedImageView` can use a single animated image URL through `SetResourceUrl`, or a sequence of still images through `SetResourceUrls`. A URL sequence is useful when an animation is delivered as numbered image frames.

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

Setting `SetResourceUrl` clears any URL sequence, and setting `SetResourceUrls` clears the single URL. Use `GetResourceUrl` when the view is configured with a single URL, and `GetResourceUrls` when it is configured with a frame sequence.

```cpp
Dali::String currentUrl = view.GetResourceUrl();
const Dali::Vector<Dali::String>& currentFrames = view.GetResourceUrls();
```

## Control Playback and Frames

Playback is controlled with `Play`, `Pause`, `Stop`, and `JumpToFrame`. Use `GetPlayState`, `GetCurrentFrame`, and `GetTotalFrame` to update application UI or logs from the current animation state.

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

`SetStopBehavior` controls which frame is shown after `Stop`. Pass a `Dali::Ui::AnimatedImage::StopBehavior` value chosen by your application.

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

## Tune Loading and Rendering

Use typed setters for loading and rendering configuration. `SetDesiredWidth` and `SetDesiredHeight` provide decode-size hints in pixels. `SetImageLoadWithViewSize` requests loading at the current view size. `SetSynchronousLoading` selects synchronous loading when the application explicitly needs it.

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

Use `SetFittingMode` and `SetSamplingMode` when the animation must fit a view area with a specific scaling policy. Use `SetImageColor` and `SetPreMultipliedAlpha` for image tinting and alpha handling.

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

For resource lifetime, configure `SetLoadPolicy` and `SetReleasePolicy` with the image policies used by your application.

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

## Use Masks, Placeholders, and Pixel Areas

`SetPlaceholderUrl` configures an image shown while the animated resource is loading. `SetAlphaMaskUrl`, `SetCropToMask`, and `SetMaskingMode` apply an alpha mask to the animated image.

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

Use `SetPixelArea` to display a normalized sub-region of the animated image. The value is `(x, y, width, height)`, with each component in the range `[0, 1]`.

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

## Handle Resource and Animation Signals

Use `ResourceReadySignal` to react when the image resource has finished loading and is ready to display. Use `AnimationFinishedSignal` to react when playback has completed its configured loop count. Both signals use a `Dali::Ui::View` callback parameter.

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

`GetLoadingStatus` returns the current visual resource status for the animated image.

```cpp
Dali::Ui::Visual::ResourceStatus status = view.GetLoadingStatus();
(void)status;
```

## Property Constants

For application code, prefer typed methods such as `SetResourceUrl`, `SetLoopCount`, `SetFrameDelay`, `SetAlphaMaskUrl`, and `SetPixelArea`. The app-facing property constants are owned by `Dali::Ui::AnimatedImageView::Property`.

Common `Dali::Ui::AnimatedImageView::Property` constants include:

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

`Dali::Ui::AnimatedImageViewPropertyIndex` defines the backing property index range for `Dali::Ui::AnimatedImageView`. For example, `Dali::Ui::AnimatedImageViewPropertyIndex::ALPHA_MASK_URL` and `Dali::Ui::AnimatedImageViewPropertyIndex::BATCH_SIZE` are the index definitions behind the corresponding `Dali::Ui::AnimatedImageView::Property` entries.
