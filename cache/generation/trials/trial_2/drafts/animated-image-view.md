---
title: Animated Image View
sidebar_label: Animated Image View
category: views-components
---

# Animated Image View

`Dali::Ui::AnimatedImageView` displays and controls animated image resources in a dali-ui application.

## Table of Contents

- [Create an Animated Image View](#create-an-animated-image-view)
- [Use a Frame URL Sequence](#use-a-frame-url-sequence)
- [Control Playback](#control-playback)
- [Configure Looping, Speed, and Stop Behavior](#configure-looping-speed-and-stop-behavior)
- [Fit, Sample, Tint, and Crop the Image](#fit-sample-tint-and-crop-the-image)
- [Loading and Resource Signals](#loading-and-resource-signals)
- [AnimatedImageView Properties](#animatedimageview-properties)

## Create an Animated Image View

Create the view with `Dali::Ui::AnimatedImageView::New()`. Pass a URL when the source is a single animated image such as GIF or WebP, or create an empty view and set the source later with `SetResourceUrl()`.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

Dali::Ui::AnimatedImageView CreateSpinner()
{
  return Dali::Ui::AnimatedImageView::New("images/loading.gif")
    .SetDesiredWidth(128)
    .SetDesiredHeight(128)
    .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO)
    .SetLoopCount(-1);
}
```

`Dali::Ui::AnimatedImageView` is a `Dali::Ui::View`, so application code should keep it in the view tree as a view object rather than treating it as a raw actor. The typed setters return `Dali::Ui::AnimatedImageView&`, which makes fluent configuration practical.

```cpp
Dali::Ui::AnimatedImageView badge = Dali::Ui::AnimatedImageView::New();

badge
  .SetResourceUrl("images/success.webp")
  .SetDesiredWidth(96)
  .SetDesiredHeight(96)
  .SetImageLoadWithViewSize(true)
  .SetLoopCount(1);
```

Use `GetResourceUrl()`, `GetDesiredWidth()`, `GetDesiredHeight()`, and `IsImageLoadWithViewSizeEnabled()` when the app needs to inspect the current configuration.

```cpp
Dali::String url = badge.GetResourceUrl();
int width        = badge.GetDesiredWidth();
int height       = badge.GetDesiredHeight();
bool sizedLoad   = badge.IsImageLoadWithViewSizeEnabled();
```

## Use a Frame URL Sequence

`SetResourceUrls()` configures a frame-by-frame animation from a `Dali::Vector<Dali::String>`. This is useful when the frames are stored as separate image files. Setting a URL sequence replaces the single-resource source configured by `SetResourceUrl()`.

```cpp
Dali::Vector<Dali::String> frames;
frames.PushBack(Dali::String("frames/progress-001.png"));
frames.PushBack(Dali::String("frames/progress-002.png"));
frames.PushBack(Dali::String("frames/progress-003.png"));
frames.PushBack(Dali::String("frames/progress-004.png"));

Dali::Ui::AnimatedImageView progress = Dali::Ui::AnimatedImageView::New()
  .SetResourceUrls(frames)
  .SetBatchSize(4)
  .SetCacheSize(8)
  .SetFrameDelay(100)
  .SetLoopCount(-1);
```

For a sequence source, `SetBatchSize()` controls how many frames are preloaded in each batch, and `SetCacheSize()` controls how many frames are kept cached. `SetFrameDelay()` sets the delay between frames in milliseconds; `GetFrameDelay()` returns the override value, or `-1` when no override is set.

```cpp
const Dali::Vector<Dali::String>& activeFrames = progress.GetResourceUrls();

int batchSize = progress.GetBatchSize();
int cacheSize = progress.GetCacheSize();
int delayMs   = progress.GetFrameDelay();
```

To return from a frame sequence to a single animated file, set an empty URL vector and then set a resource URL.

```cpp
progress
  .SetResourceUrls(Dali::Vector<Dali::String>{})
  .SetResourceUrl("images/progress.webp");
```

## Control Playback

Playback is controlled with `Play()`, `Pause()`, `Stop()`, and `JumpToFrame()`. These methods operate on the `Dali::Ui::AnimatedImageView` handle and return the view for chaining.

```cpp
void StartAnimation(Dali::Ui::AnimatedImageView view)
{
  view.Play();
}

void PauseAnimation(Dali::Ui::AnimatedImageView view)
{
  view.Pause();
}

void RestartAnimation(Dali::Ui::AnimatedImageView view)
{
  view.Stop()
      .JumpToFrame(0)
      .Play();
}
```

Use `GetPlayState()`, `GetCurrentFrame()`, and `GetTotalFrame()` to report or synchronize UI state.

```cpp
void TogglePlayback(Dali::Ui::AnimatedImageView view)
{
  if(view.GetPlayState() == Dali::Ui::AnimatedImage::PlayState::PLAYING)
  {
    view.Pause();
  }
  else
  {
    view.Play();
  }
}

int currentFrame = view.GetCurrentFrame();
int totalFrames  = view.GetTotalFrame();
```

`AnimationFinishedSignal()` is emitted when the animation completes all configured loops. The callback receives the finished `Dali::Ui::View`.

```cpp
class AnimationController : public Dali::ConnectionTracker
{
public:
  void Attach(Dali::Ui::AnimatedImageView view)
  {
    mView = view;
    mView.AnimationFinishedSignal().Connect(this, &AnimationController::OnAnimationFinished);
  }

private:
  void OnAnimationFinished(Dali::Ui::View view)
  {
    mView.Stop();
  }

  Dali::Ui::AnimatedImageView mView;
};
```

## Configure Looping, Speed, and Stop Behavior

Use `SetLoopCount()` to decide how many times playback repeats. `-1` means infinite looping, `0` prevents playback, and positive values request an exact loop count.

```cpp
Dali::Ui::AnimatedImageView intro = Dali::Ui::AnimatedImageView::New("images/intro.gif")
  .SetLoopCount(1)
  .SetFrameSpeedFactor(1.0f)
  .SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::LAST_FRAME);
```

`SetFrameSpeedFactor()` applies a playback speed multiplier. Values below `1.0f` slow playback down, and values above `1.0f` speed it up.

```cpp
void UseReducedMotion(Dali::Ui::AnimatedImageView view, bool reducedMotion)
{
  if(reducedMotion)
  {
    view.SetFrameSpeedFactor(0.5f);
  }
  else
  {
    view.SetFrameSpeedFactor(1.0f);
  }
}
```

`SetStopBehavior()` controls which frame is displayed after `Stop()` or after a finite loop completes. Use `Dali::Ui::AnimatedImage::StopBehavior::CURRENT_FRAME`, `FIRST_FRAME`, or `LAST_FRAME`.

```cpp
void ConfigureCompletionFrame(Dali::Ui::AnimatedImageView view)
{
  view
    .SetLoopCount(2)
    .SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::FIRST_FRAME)
    .Play();
}

int loopCount = view.GetLoopCount();
float speed   = view.GetFrameSpeedFactor();
Dali::Ui::AnimatedImage::StopBehavior stopBehavior = view.GetStopBehavior();
```

## Fit, Sample, Tint, and Crop the Image

`AnimatedImageView` exposes image-display controls through typed setters. Use `SetFittingMode()` to choose how the image fits inside the view bounds, and `SetSamplingMode()` to choose the scaling filter.

```cpp
Dali::Ui::AnimatedImageView avatar = Dali::Ui::AnimatedImageView::New("images/avatar-animated.webp")
  .SetDesiredWidth(160)
  .SetDesiredHeight(160)
  .SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO)
  .SetSamplingMode(Dali::Ui::Image::SamplingMode::LINEAR);
```

Use `SetImageColor()` for tinting and `SetPreMultipliedAlpha()` when the source content is prepared for premultiplied alpha rendering.

```cpp
avatar
  .SetImageColor(Dali::Ui::UiColor(0xFFFFFFFF))
  .SetPreMultipliedAlpha(true);

Dali::Ui::UiColor color = avatar.GetImageColor();
bool premultiplied      = avatar.IsPreMultipliedAlpha();
```

Use `SetAlphaMaskUrl()`, `SetCropToMask()`, and `SetMaskingMode()` when the animated image should be clipped by an alpha mask.

```cpp
Dali::Ui::AnimatedImageView masked = Dali::Ui::AnimatedImageView::New("images/animated-card.webp")
  .SetAlphaMaskUrl("images/card-mask.png")
  .SetCropToMask(true)
  .SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING)
  .SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);
```

`SetPixelArea()` displays a normalized sub-region of the image, using `(x, y, width, height)`.

```cpp
masked.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.5f, 1.0f));

Dali::Vector4 pixelArea = masked.GetPixelArea();
```

## Loading and Resource Signals

Use `SetPlaceholderUrl()` to show a still image while the animated resource is loading. Use `ResourceReadySignal()` to know when the resource is ready to display.

```cpp
class LoadingController : public Dali::ConnectionTracker
{
public:
  Dali::Ui::AnimatedImageView Create()
  {
    mView = Dali::Ui::AnimatedImageView::New("images/large-animation.webp")
      .SetPlaceholderUrl("images/placeholder.png")
      .SetLoadPolicy(Dali::Ui::Image::LoadPolicy::ATTACHED)
      .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DETACHED);

    mView.ResourceReadySignal().Connect(this, &LoadingController::OnResourceReady);
    return mView;
  }

private:
  void OnResourceReady(Dali::Ui::View view)
  {
    mView.Play();
  }

  Dali::Ui::AnimatedImageView mView;
};
```

`SetSynchronousLoading()` requests synchronous loading, while `SetLoadPolicy()` and `SetReleasePolicy()` control when resources are loaded and released. `GetLoadingStatus()` returns the current `Dali::Ui::Visual::ResourceStatus`.

```cpp
Dali::Ui::AnimatedImageView icon = Dali::Ui::AnimatedImageView::New("images/icon-animated.gif")
  .SetSynchronousLoading(false)
  .SetLoadPolicy(Dali::Ui::Image::LoadPolicy::IMMEDIATE)
  .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::NEVER);

bool synchronous = icon.IsSynchronousLoading();
Dali::Ui::Image::LoadPolicy loadPolicy = icon.GetLoadPolicy();
Dali::Ui::Image::ReleasePolicy releasePolicy = icon.GetReleasePolicy();
Dali::Ui::Visual::ResourceStatus status = icon.GetLoadingStatus();
```

## AnimatedImageView Properties

Prefer typed setters and getters for application code. `Dali::Ui::AnimatedImageView::Property` owns the property constants for this view, and `Dali::Ui::AnimatedImageViewPropertyIndex` is the underlying property-index definition used by the view implementation and property registration.

| Owned property constant | Meaning |
| --- | --- |
| `Dali::Ui::AnimatedImageView::Property::IMAGE` | Single animated image URL |
| `Dali::Ui::AnimatedImageView::Property::IMAGE_URLS` | Frame URL array |
| `Dali::Ui::AnimatedImageView::Property::LOOP_COUNT` | Loop count, with `-1` for infinite looping |
| `Dali::Ui::AnimatedImageView::Property::STOP_BEHAVIOR` | Stop behavior as `Dali::Ui::AnimatedImage::StopBehavior` |
| `Dali::Ui::AnimatedImageView::Property::FRAME_SPEED_FACTOR` | Playback speed multiplier |
| `Dali::Ui::AnimatedImageView::Property::BATCH_SIZE` | Frame preload batch size |
| `Dali::Ui::AnimatedImageView::Property::CACHE_SIZE` | Frame cache size |
| `Dali::Ui::AnimatedImageView::Property::FRAME_DELAY` | Frame delay override in milliseconds |
| `Dali::Ui::AnimatedImageView::Property::IMAGE_COLOR` | Image tint color |
| `Dali::Ui::AnimatedImageView::Property::PRE_MULTIPLIED_ALPHA` | Premultiplied alpha flag |
| `Dali::Ui::AnimatedImageView::Property::FITTING_MODE` | Image fitting mode |
| `Dali::Ui::AnimatedImageView::Property::SAMPLING_MODE` | Scaling sampling mode |
| `Dali::Ui::AnimatedImageView::Property::DESIRED_WIDTH` | Desired decode width hint |
| `Dali::Ui::AnimatedImageView::Property::DESIRED_HEIGHT` | Desired decode height hint |
| `Dali::Ui::AnimatedImageView::Property::LOAD_POLICY` | Resource load policy |
| `Dali::Ui::AnimatedImageView::Property::RELEASE_POLICY` | Resource release policy |
| `Dali::Ui::AnimatedImageView::Property::SYNCHRONOUS_LOADING` | Synchronous loading flag |
| `Dali::Ui::AnimatedImageView::Property::IMAGE_LOAD_WITH_VIEW_SIZE` | Load using the current view size |
| `Dali::Ui::AnimatedImageView::Property::ALPHA_MASK_URL` | Alpha mask image URL |
| `Dali::Ui::AnimatedImageView::Property::CROP_TO_MASK` | Crop-to-mask flag |
| `Dali::Ui::AnimatedImageView::Property::MASKING_MODE` | Alpha masking mode |
| `Dali::Ui::AnimatedImageView::Property::PLACEHOLDER_IMAGE` | Placeholder image URL |
| `Dali::Ui::AnimatedImageView::Property::PIXEL_AREA` | Normalized image sub-region |

`Dali::Ui::AnimatedImageViewPropertyIndex::ALPHA_MASK_URL` and `Dali::Ui::AnimatedImageViewPropertyIndex::BATCH_SIZE` are part of the same owned property-index surface. Application code normally reaches them through `Dali::Ui::AnimatedImageView::Property::ALPHA_MASK_URL` and `Dali::Ui::AnimatedImageView::Property::BATCH_SIZE`, while using `SetAlphaMaskUrl()` and `SetBatchSize()` for type-safe configuration.
