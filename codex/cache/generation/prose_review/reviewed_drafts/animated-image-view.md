---
title: Animated Image View
sidebar_label: Animated Image View
category: views-components
---

# Animated Image View

`Dali::Ui::AnimatedImageView` displays animated image resources and provides playback control in a dali-ui application.

## Table of Contents

- [Create an Animated Image View](#create-an-animated-image-view)
- [Play, Pause, Stop, and Seek](#play-pause-stop-and-seek)
- [Use a Frame Sequence](#use-a-frame-sequence)
- [Tune Frame Loading and Playback](#tune-frame-loading-and-playback)
- [Fit, Sample, Tint, and Crop](#fit-sample-tint-and-crop)
- [Mask and Placeholder Images](#mask-and-placeholder-images)
- [Loading Policies and Status](#loading-policies-and-status)
- [Signals](#signals)
- [Property Constants](#property-constants)

## Create an Animated Image View

Use `Dali::Ui::AnimatedImageView::New` to create the view. The returned `Dali::Ui::AnimatedImageView` derives from `Dali::Ui::View`, so application code can place it in the normal dali-ui view tree.

```cpp
Dali::Ui::AnimatedImageView CreateLoadingImage(const Dali::String& url)
{
  return Dali::Ui::AnimatedImageView::New(url)
    .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO)
    .SetLoopCount(-1)
    .SetFrameSpeedFactor(1.0f);
}
```

You can also create the view first and set or replace the resource later with `SetResourceUrl`.

```cpp
Dali::Ui::AnimatedImageView image = Dali::Ui::AnimatedImageView::New();

image
  .SetResourceUrl("images/spinner.gif")
  .SetLoopCount(-1)
  .Play();
```

Read the configured source with `GetResourceUrl`.

```cpp
Dali::String currentUrl = image.GetResourceUrl();
```

## Play, Pause, Stop, and Seek

`Play`, `Pause`, and `Stop` control playback. `JumpToFrame` moves the animation to a specific frame index. Use `GetPlayState`, `GetCurrentFrame`, and `GetTotalFrame` when updating controls or status UI.

```cpp
void StartPreview(Dali::Ui::AnimatedImageView image)
{
  image
    .SetLoopCount(3)
    .SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::LAST_FRAME)
    .Play();
}

void PausePreview(Dali::Ui::AnimatedImageView image)
{
  image.Pause();
}

void RestartPreview(Dali::Ui::AnimatedImageView image)
{
  image
    .Stop()
    .JumpToFrame(0)
    .Play();
}
```

`SetLoopCount(-1)` loops indefinitely. `SetLoopCount(0)` prevents playback. Positive values play an exact number of loops.

```cpp
void ConfigureLooping(Dali::Ui::AnimatedImageView image, bool repeat)
{
  image.SetLoopCount(repeat ? -1 : 1);
}
```

Use `SetStopBehavior` to choose which frame remains visible after `Stop`.

```cpp
image.SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::CURRENT_FRAME);
image.SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::FIRST_FRAME);
image.SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::LAST_FRAME);
```

## Use a Frame Sequence

`SetResourceUrls` configures an ordered list of image files as animation frames. This is useful when the animation is delivered as numbered PNG or WebP frames instead of a single GIF or WebP file.

```cpp
void UseFrameSequence(
  Dali::Ui::AnimatedImageView image,
  const Dali::Vector<Dali::String>& frameUrls)
{
  image
    .SetResourceUrls(frameUrls)
    .SetBatchSize(4)
    .SetCacheSize(10)
    .SetFrameDelay(80)
    .SetLoopCount(-1)
    .Play();
}
```

`SetResourceUrls` overrides the single URL set by `SetResourceUrl`. To return to a single animated file, set the URL with `SetResourceUrl`.

```cpp
void UseSingleAnimatedFile(Dali::Ui::AnimatedImageView image, const Dali::String& url)
{
  image
    .SetResourceUrl(url)
    .SetFrameDelay(-1)
    .Play();
}
```

Use `GetResourceUrls` when the app needs to inspect the current frame sequence.

```cpp
const Dali::Vector<Dali::String>& frames = image.GetResourceUrls();
```

## Tune Frame Loading and Playback

`SetFrameSpeedFactor` changes playback speed. Values below `1.0f` slow playback, and values above `1.0f` speed it up.

```cpp
void SetPreviewSpeed(Dali::Ui::AnimatedImageView image, float speed)
{
  image.SetFrameSpeedFactor(speed);
}
```

`SetFrameDelay` overrides the frame delay in milliseconds. Use `-1` to use the resource's own frame timing.

```cpp
image.SetFrameDelay(200);
int delay = image.GetFrameDelay();

image.SetFrameDelay(-1);
```

For frame sequences, `SetBatchSize` controls how many frames are loaded per batch, and `SetCacheSize` controls how many frames are kept in cache.

```cpp
Dali::Ui::AnimatedImageView CreateSequencePlayer(
  const Dali::Vector<Dali::String>& frameUrls)
{
  return Dali::Ui::AnimatedImageView::New()
    .SetResourceUrls(frameUrls)
    .SetBatchSize(4)
    .SetCacheSize(12)
    .SetLoopCount(-1);
}
```

The matching getters let app code reflect the current tuning in developer tools or settings UI.

```cpp
int batchSize = image.GetBatchSize();
int cacheSize = image.GetCacheSize();
float speed = image.GetFrameSpeedFactor();
```

## Fit, Sample, Tint, and Crop

Use `SetFittingMode` to control how frames fit inside the view bounds. Common values are `Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO`, `Dali::Ui::Image::FittingMode::FILL`, `Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO`, and `Dali::Ui::Image::FittingMode::CENTER`.

```cpp
Dali::Ui::AnimatedImageView thumbnail =
  Dali::Ui::AnimatedImageView::New("images/preview.gif")
    .SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO)
    .SetLoopCount(-1)
    .Play();
```

Use `SetSamplingMode` when scaling quality matters.

```cpp
image.SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX_THEN_LINEAR);
Dali::Ui::Image::SamplingMode sampling = image.GetSamplingMode();
```

Use `SetDesiredWidth` and `SetDesiredHeight` as loader hints when the displayed animation does not need the original image resolution.

```cpp
image
  .SetDesiredWidth(320)
  .SetDesiredHeight(180)
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
```

Use `SetImageColor` to multiply the image by a color, including alpha.

```cpp
image.SetImageColor(Dali::Ui::UiColor(0xFFFFFFFF));
Dali::Ui::UiColor color = image.GetImageColor();
```

Use `SetPixelArea` to show a normalized sub-region of the animated image.

```cpp
image.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.5f, 1.0f));
Dali::Vector4 area = image.GetPixelArea();
```

## Mask and Placeholder Images

`SetAlphaMaskUrl` applies an alpha mask image. `SetCropToMask` controls whether the output is cropped to the mask bounds. `SetMaskingMode` selects whether masking is applied during loading or rendering.

```cpp
Dali::Ui::AnimatedImageView masked =
  Dali::Ui::AnimatedImageView::New("images/badge.gif")
    .SetAlphaMaskUrl("images/circle-mask.png")
    .SetCropToMask(true)
    .SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING)
    .SetLoopCount(-1)
    .Play();
```

For a loader-time mask, use `Dali::Ui::Image::MaskingType::MASKING_ON_LOADING`.

```cpp
image.SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_LOADING);
```

A placeholder image can be shown while the animated resource loads. It is removed when the main image resource becomes ready.

```cpp
Dali::Ui::AnimatedImageView delayed =
  Dali::Ui::AnimatedImageView::New()
    .SetPlaceholderUrl("images/placeholder.png")
    .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

delayed
  .SetResourceUrl("images/large-animation.gif")
  .Play();
```

Use the matching getters when synchronizing editor panels or view state.

```cpp
Dali::String maskUrl = image.GetAlphaMaskUrl();
bool cropToMask = image.IsCropToMask();
Dali::String placeholderUrl = image.GetPlaceholderUrl();
```

## Loading Policies and Status

`SetLoadPolicy` controls when the animated image resource is loaded. `Dali::Ui::Image::LoadPolicy::ATTACHED` loads when the view is attached to the scene, and `Dali::Ui::Image::LoadPolicy::IMMEDIATE` loads as soon as the visual is configured.

```cpp
Dali::Ui::AnimatedImageView image =
  Dali::Ui::AnimatedImageView::New("images/intro.gif")
    .SetLoadPolicy(Dali::Ui::Image::LoadPolicy::IMMEDIATE)
    .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::NEVER);
```

`SetReleasePolicy` controls when texture resources are released. Available values are `Dali::Ui::Image::ReleasePolicy::DETACHED`, `Dali::Ui::Image::ReleasePolicy::DESTROYED`, and `Dali::Ui::Image::ReleasePolicy::NEVER`.

```cpp
void ConfigureCachePolicy(Dali::Ui::AnimatedImageView image, bool keepLoaded)
{
  image.SetReleasePolicy(
    keepLoaded
      ? Dali::Ui::Image::ReleasePolicy::NEVER
      : Dali::Ui::Image::ReleasePolicy::DETACHED);
}
```

`SetSynchronousLoading` enables synchronous loading. `SetImageLoadWithViewSize` requests loading at the current view size.

```cpp
image
  .SetSynchronousLoading(false)
  .SetImageLoadWithViewSize(true);

bool synchronous = image.IsSynchronousLoading();
bool loadWithViewSize = image.IsImageLoadWithViewSizeEnabled();
```

Use `GetLoadingStatus` to inspect the resource state.

```cpp
Dali::Ui::Visual::ResourceStatus status = image.GetLoadingStatus();
```

## Signals

`ResourceReadySignal` is emitted when the image resource is ready. The callback receives the ready `Dali::Ui::View`.

```cpp
class ImageController
{
public:
  void Connect(Dali::Ui::AnimatedImageView image)
  {
    image.ResourceReadySignal().Connect(this, &ImageController::OnResourceReady);
  }

  void OnResourceReady(Dali::Ui::View view)
  {
    Dali::Ui::AnimatedImageView image = Dali::Ui::AnimatedImageView::DownCast(view);
    if(image)
    {
      image.Play();
    }
  }
};
```

`AnimationFinishedSignal` is emitted when playback completes all configured loops.

```cpp
class PlaybackController
{
public:
  void Connect(Dali::Ui::AnimatedImageView image)
  {
    image.AnimationFinishedSignal().Connect(this, &PlaybackController::OnAnimationFinished);
  }

  void OnAnimationFinished(Dali::Ui::View view)
  {
    Dali::Ui::AnimatedImageView image = Dali::Ui::AnimatedImageView::DownCast(view);
    if(image)
    {
      image.Stop();
    }
  }
};
```

Use `DownCast` when a callback receives a generic `Dali::Ui::View` and the handler needs `Dali::Ui::AnimatedImageView` methods.

## Property Constants

Prefer typed setters such as `SetResourceUrl`, `SetLoopCount`, `SetFittingMode`, and `SetPlaceholderUrl` in application code. `Dali::Ui::AnimatedImageView::Property` provides property constants for frameworks that need property indices.

| Property constant | Typed API |
| --- | --- |
| `Dali::Ui::AnimatedImageView::Property::IMAGE` | `SetResourceUrl`, `GetResourceUrl` |
| `Dali::Ui::AnimatedImageView::Property::IMAGE_URLS` | `SetResourceUrls`, `GetResourceUrls` |
| `Dali::Ui::AnimatedImageView::Property::LOOP_COUNT` | `SetLoopCount`, `GetLoopCount` |
| `Dali::Ui::AnimatedImageView::Property::STOP_BEHAVIOR` | `SetStopBehavior`, `GetStopBehavior` |
| `Dali::Ui::AnimatedImageView::Property::FRAME_SPEED_FACTOR` | `SetFrameSpeedFactor`, `GetFrameSpeedFactor` |
| `Dali::Ui::AnimatedImageView::Property::BATCH_SIZE` | `SetBatchSize`, `GetBatchSize` |
| `Dali::Ui::AnimatedImageView::Property::CACHE_SIZE` | `SetCacheSize`, `GetCacheSize` |
| `Dali::Ui::AnimatedImageView::Property::FRAME_DELAY` | `SetFrameDelay`, `GetFrameDelay` |
| `Dali::Ui::AnimatedImageView::Property::IMAGE_COLOR` | `SetImageColor`, `GetImageColor` |
| `Dali::Ui::AnimatedImageView::Property::PRE_MULTIPLIED_ALPHA` | `SetPreMultipliedAlpha`, `IsPreMultipliedAlpha` |
| `Dali::Ui::AnimatedImageView::Property::FITTING_MODE` | `SetFittingMode`, `GetFittingMode` |
| `Dali::Ui::AnimatedImageView::Property::SAMPLING_MODE` | `SetSamplingMode`, `GetSamplingMode` |
| `Dali::Ui::AnimatedImageView::Property::DESIRED_WIDTH` | `SetDesiredWidth`, `GetDesiredWidth` |
| `Dali::Ui::AnimatedImageView::Property::DESIRED_HEIGHT` | `SetDesiredHeight`, `GetDesiredHeight` |
| `Dali::Ui::AnimatedImageView::Property::LOAD_POLICY` | `SetLoadPolicy`, `GetLoadPolicy` |
| `Dali::Ui::AnimatedImageView::Property::RELEASE_POLICY` | `SetReleasePolicy`, `GetReleasePolicy` |
| `Dali::Ui::AnimatedImageView::Property::SYNCHRONOUS_LOADING` | `SetSynchronousLoading`, `IsSynchronousLoading` |
| `Dali::Ui::AnimatedImageView::Property::IMAGE_LOAD_WITH_VIEW_SIZE` | `SetImageLoadWithViewSize`, `IsImageLoadWithViewSizeEnabled` |
| `Dali::Ui::AnimatedImageView::Property::ALPHA_MASK_URL` | `SetAlphaMaskUrl`, `GetAlphaMaskUrl` |
| `Dali::Ui::AnimatedImageView::Property::CROP_TO_MASK` | `SetCropToMask`, `IsCropToMask` |
| `Dali::Ui::AnimatedImageView::Property::MASKING_MODE` | `SetMaskingMode`, `GetMaskingMode` |
| `Dali::Ui::AnimatedImageView::Property::PLACEHOLDER_IMAGE` | `SetPlaceholderUrl`, `GetPlaceholderUrl` |
| `Dali::Ui::AnimatedImageView::Property::PIXEL_AREA` | `SetPixelArea`, `GetPixelArea` |

`Dali::Ui::AnimatedImageViewPropertyIndex` defines the underlying property indices used by `Dali::Ui::AnimatedImageView::Property`. For app code, use the `Dali::Ui::AnimatedImageView::Property` owner when a property index is required.
