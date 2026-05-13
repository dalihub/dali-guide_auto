---
title: Visuals
sidebar_label: Visuals
category: images-visuals
---

# Visuals

Visuals are reusable dali-ui rendering handles for color fills, images, animated images, and Lottie animation content in a `Dali::Ui::View`-based application.

## Table of Contents

- [Choosing a Visual Type](#choosing-a-visual-type)
- [Configuring Image Animation](#configuring-image-animation)
- [Playback Control](#playback-control)
- [Reading Visual State](#reading-visual-state)
- [Property Owners](#property-owners)

## Choosing a Visual Type

Use `Dali::Ui::VisualBase` as the common handle type when your code needs to keep visuals in a single collection or pass them through a view-level visual pipeline. Concrete visual classes own the feature-specific setup:

- `Dali::Ui::ColorVisual` renders a color-based visual.
- `Dali::Ui::ImageVisual` renders image content.
- `Dali::Ui::AnimatedImageVisual` renders animated image content from one animated resource or from a sequence of image resources.
- `Dali::Ui::LottieAnimationVisual` renders Lottie animation content.
- `Dali::Ui::VisualsContainer` exposes the view-owned visual container used for a specific visual range.

Application code should treat `Dali::Ui::View` as the object model that owns and displays visuals. `Dali::Ui::VisualBase` and its derived visual handles describe what to render; `Dali::Ui::View::AddVisual` or the fluent `Dali::Ui::View::Visuals` API decides where that visual appears in the view tree.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

AnimatedImageVisual CreateLoadingVisual()
{
  return AnimatedImageVisual::New()
    .SetResourceUrl("assets/loading.gif")
    .SetDesiredWidth(96)
    .SetDesiredHeight(96)
    .SetEnableBrokenImage(true)
    .SetLoopCount(-1)
    .SetFrameDelay(80)
    .Play();
}

VisualBase CreateReusableVisualHandle()
{
  AnimatedImageVisual visual = CreateLoadingVisual();
  VisualBase baseVisual = visual;
  return baseVisual;
}
```

`Dali::Ui::Internal::VisualBaseImpl` and `Dali::Ui::Internal::Visual::Transform` are implementation-facing types. Application code normally works with `Dali::Ui::VisualBase`, `Dali::Ui::ImageVisual`, `Dali::Ui::ColorVisual`, `Dali::Ui::AnimatedImageVisual`, and `Dali::Ui::LottieAnimationVisual` instead.

## Configuring Image Animation

`Dali::Ui::AnimatedImageVisual::New` creates an animated image visual. The typed setters return `Dali::Ui::AnimatedImageVisual&`, so they can be chained for concise setup.

Use `Dali::Ui::AnimatedImageVisual::SetResourceUrl` for one animated image resource, or `Dali::Ui::AnimatedImageVisual::SetResourceUrlList` for an image sequence. Use sizing setters such as `Dali::Ui::AnimatedImageVisual::SetDesiredWidth` and `Dali::Ui::AnimatedImageVisual::SetDesiredHeight` when the visual should request a specific resource size. Use playback-related setters such as `Dali::Ui::AnimatedImageVisual::SetFrameDelay`, `Dali::Ui::AnimatedImageVisual::SetFrameSpeedFactor`, and `Dali::Ui::AnimatedImageVisual::SetLoopCount` to tune animation behavior.

```cpp
AnimatedImageVisual CreateAvatarAnimation()
{
  AnimatedImageVisual visual = AnimatedImageVisual::New()
    .SetResourceUrlList({
      "assets/avatar-01.png",
      "assets/avatar-02.png",
      "assets/avatar-03.png",
    })
    .SetDesiredWidth(240)
    .SetDesiredHeight(240)
    .SetFrameDelay(100)
    .SetFrameSpeedFactor(1.0f)
    .SetLoopCount(-1)
    .SetBatchSize(2)
    .SetCacheSize(4)
    .SetEnableBrokenImage(true);

  return visual;
}
```

Use alpha masking when the visual content needs to be clipped by a mask image. `Dali::Ui::AnimatedImageVisual::SetAlphaMaskUrl` sets the mask resource, `Dali::Ui::AnimatedImageVisual::SetMaskContentScale` controls mask scaling, and `Dali::Ui::AnimatedImageVisual::SetCropToMask` controls whether the visual is cropped to the mask.

```cpp
AnimatedImageVisual CreateMaskedAnimation()
{
  return AnimatedImageVisual::New()
    .SetResourceUrl("assets/card-animation.gif")
    .SetDesiredWidth(320)
    .SetDesiredHeight(180)
    .SetAlphaMaskUrl("assets/masks/card-mask.png")
    .SetMaskContentScale(1.0f)
    .SetCropToMask(true)
    .SetLoopCount(-1)
    .Play();
}
```

## Playback Control

`Dali::Ui::AnimatedImageVisual::Play`, `Dali::Ui::AnimatedImageVisual::Pause`, `Dali::Ui::AnimatedImageVisual::Stop`, and `Dali::Ui::AnimatedImageVisual::JumpTo` control the playback state of an animated image visual. They return the same visual handle, so command-style usage and chaining are both supported.

```cpp
void StartIntro(AnimatedImageVisual& visual)
{
  visual
    .JumpTo(0)
    .SetFrameSpeedFactor(1.25f)
    .Play();
}

void PauseIntro(AnimatedImageVisual& visual)
{
  visual.Pause();
}

void ReplayFromFrame(AnimatedImageVisual& visual, int frame)
{
  visual
    .JumpTo(frame)
    .Play();
}
```

For fixed-length effects, use `Dali::Ui::AnimatedImageVisual::SetLoopCount` with a positive loop count. For a continuously running visual, use `-1` for infinite looping and keep the visual controlled through `Play`, `Pause`, `Stop`, and `JumpTo`; a loop count of `0` means the animation will not play.

```cpp
AnimatedImageVisual CreateOneShotEffect()
{
  return AnimatedImageVisual::New()
    .SetResourceUrl("assets/effect.gif")
    .SetDesiredWidth(128)
    .SetDesiredHeight(128)
    .SetLoopCount(1)
    .SetFrameDelay(60);
}
```

## Reading Visual State

Getter APIs mirror the typed setup APIs. Use them when app code needs to inspect visual configuration or report animation progress.

`Dali::Ui::AnimatedImageVisual::GetCurrentFrameNumber` returns the current frame number, and `Dali::Ui::AnimatedImageVisual::GetTotalFrameNumber` returns the total frame count, or `-1` before image decoding has completed. `Dali::Ui::AnimatedImageVisual::GetPlayState` reports the current play state.

```cpp
struct AnimationSnapshot
{
  int currentFrame;
  int totalFrames;
  int desiredWidth;
  int desiredHeight;
  int frameDelay;
  float speed;
};

AnimationSnapshot SnapshotAnimation(const AnimatedImageVisual& visual)
{
  AnimationSnapshot snapshot;
  snapshot.currentFrame = visual.GetCurrentFrameNumber();
  snapshot.totalFrames = visual.GetTotalFrameNumber();
  snapshot.desiredWidth = visual.GetDesiredWidth();
  snapshot.desiredHeight = visual.GetDesiredHeight();
  snapshot.frameDelay = visual.GetFrameDelay();
  snapshot.speed = visual.GetFrameSpeedFactor();
  return snapshot;
}
```

Configuration getters are useful for validating resource policy before a visual is reused.

```cpp
bool UsesMask(const AnimatedImageVisual& visual)
{
  return !visual.GetAlphaMaskUrl().empty() && visual.IsCropToMask();
}

bool HasExplicitSize(const AnimatedImageVisual& visual)
{
  return visual.GetDesiredWidth() > 0 && visual.GetDesiredHeight() > 0;
}
```

## Property Owners

Each visual type owns its own property constants. Use the property owner that matches the concrete visual type.

`Dali::Ui::AnimatedImageVisual::Property` owns animated image properties such as `Dali::Ui::AnimatedImageVisual::Property::URL`, `Dali::Ui::AnimatedImageVisual::Property::FRAME_DELAY`, `Dali::Ui::AnimatedImageVisual::Property::FRAME_SPEED_FACTOR`, `Dali::Ui::AnimatedImageVisual::Property::LOOP_COUNT`, `Dali::Ui::AnimatedImageVisual::Property::PLAY_STATE`, `Dali::Ui::AnimatedImageVisual::Property::CURRENT_FRAME_NUMBER`, and `Dali::Ui::AnimatedImageVisual::Property::TOTAL_FRAME_NUMBER`.

```cpp
int AnimatedImageUrlProperty()
{
  return AnimatedImageVisual::Property::URL;
}

int AnimatedImageFrameDelayProperty()
{
  return AnimatedImageVisual::Property::FRAME_DELAY;
}

int AnimatedImagePlayStateProperty()
{
  return AnimatedImageVisual::Property::PLAY_STATE;
}
```

`Dali::Ui::ImageVisual::Property` owns image properties such as `Dali::Ui::ImageVisual::Property::URL`, `Dali::Ui::ImageVisual::Property::DESIRED_WIDTH`, `Dali::Ui::ImageVisual::Property::DESIRED_HEIGHT`, `Dali::Ui::ImageVisual::Property::PIXEL_AREA`, `Dali::Ui::ImageVisual::Property::ALPHA_MASK_URL`, `Dali::Ui::ImageVisual::Property::CROP_TO_MASK`, `Dali::Ui::ImageVisual::Property::BORDER`, and `Dali::Ui::ImageVisual::Property::BORDER_ONLY`.

```cpp
int ImageUrlProperty()
{
  return ImageVisual::Property::URL;
}

int ImageMaskProperty()
{
  return ImageVisual::Property::ALPHA_MASK_URL;
}
```

`Dali::Ui::ColorVisual::Property` owns color visual properties: `Dali::Ui::ColorVisual::Property::BLUR_RADIUS` and `Dali::Ui::ColorVisual::Property::CUTOUT_POLICY`.

```cpp
int ColorBlurProperty()
{
  return ColorVisual::Property::BLUR_RADIUS;
}
```

`Dali::Ui::LottieAnimationVisual::Property` owns Lottie animation properties such as `Dali::Ui::LottieAnimationVisual::Property::URL`, `Dali::Ui::LottieAnimationVisual::Property::PLAY_STATE`, `Dali::Ui::LottieAnimationVisual::Property::CURRENT_FRAME_NUMBER`, `Dali::Ui::LottieAnimationVisual::Property::TOTAL_FRAME_NUMBER`, `Dali::Ui::LottieAnimationVisual::Property::LOOP_COUNT`, `Dali::Ui::LottieAnimationVisual::Property::CONTENT_INFO`, and `Dali::Ui::LottieAnimationVisual::Property::MARKER_INFO`.

```cpp
int LottieUrlProperty()
{
  return LottieAnimationVisual::Property::URL;
}

int LottieMarkerInfoProperty()
{
  return LottieAnimationVisual::Property::MARKER_INFO;
}
```
