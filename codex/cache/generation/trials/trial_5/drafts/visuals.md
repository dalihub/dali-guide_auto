---
title: Visuals
sidebar_label: Visuals
category: images-visuals
---

# Visuals

Visuals are lightweight dali-ui drawing objects that you attach to `Dali::Ui::View` objects to render images, colors, animated image content, and Lottie animation content.

## Table of Contents

- [Choosing a Visual Type](#choosing-a-visual-type)
- [Adding Visuals to a View](#adding-visuals-to-a-view)
- [Image Visuals](#image-visuals)
- [Animated Image Visuals](#animated-image-visuals)
- [Color Visuals](#color-visuals)
- [Lottie Animation Visuals](#lottie-animation-visuals)
- [Inspecting Visual State](#inspecting-visual-state)

## Choosing a Visual Type

Use `Dali::Ui::ImageVisual` for a single image resource, `Dali::Ui::AnimatedImageVisual` for GIF/WebP or image-sequence animation, `Dali::Ui::ColorVisual` for colored fills and decorative visual layers, and `Dali::Ui::LottieAnimationVisual` for Lottie animation content.

`Dali::Ui::VisualBase` is the common visual handle type. In application code, keep visuals attached to a `Dali::Ui::View` rather than building a raw actor tree.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

namespace
{
constexpr const char* IMAGE_URL = "/opt/usr/apps/example/res/card.png";
constexpr const char* GIF_URL   = "/opt/usr/apps/example/res/loading.gif";
} // namespace

void BuildVisualHandles()
{
  ImageVisual image = ImageVisual::New()
                        .SetResourceUrl(IMAGE_URL);

  AnimatedImageVisual spinner = AnimatedImageVisual::New()
                                  .SetResourceUrl(GIF_URL)
                                  .SetLoopCount(0)
                                  .SetCacheSize(4)
                                  .SetBatchSize(2);

  ColorVisual tint = ColorVisual::New();

  VisualBase asBase = spinner;
}
```

The property namespaces are type-specific. Use `Dali::Ui::ImageVisual::Property` for image properties, `Dali::Ui::AnimatedImageVisual::Property` for animated image properties, `Dali::Ui::ColorVisual::Property` for color visual properties, and `Dali::Ui::LottieAnimationVisual::Property` for Lottie animation properties.

## Adding Visuals to a View

A visual becomes part of the scene when it is added to a `Dali::Ui::View`. dali-ui samples attach visuals to views with `Dali::Ui::View::AddVisual` or the fluent `Dali::Ui::View::Visuals` helper, then add the view to the window or another layout.

```cpp
class VisualExample : public ConnectionTracker
{
public:
  explicit VisualExample(Application& application)
  : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &VisualExample::OnInit);
  }

private:
  void OnInit(Application application)
  {
    Window window = application.GetWindow();
    window.SetBackgroundColor(UiColor(0xFFFFFF));

    mContent = Label::New("Visual content")
                 .SetRequestedWidth(MATCH_PARENT)
                 .SetRequestedHeight(MATCH_PARENT);

    mContent.AddVisual(
      AnimatedImageVisual::New()
        .SetResourceUrl("/opt/usr/apps/example/res/loading.gif")
        .SetLoopCount(0)
        .SetCacheSize(3)
        .Play(),
      Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT);

    window.Add(mContent);
  }

  Application& mApplication;
  View         mContent;
};
```

Use view-facing APIs such as `Dali::Ui::View::AddVisual`, `Dali::Ui::View::RemoveVisual`, `Dali::Ui::View::GetVisualAt`, and `Dali::Ui::View::GetVisualCount` when your UI needs to manage a dynamic set of visuals. `AddVisual` returns `false` if the visual cannot be added, and moving an already attached visual to another view detaches it from its previous owner.

```cpp
void RemoveLastContentVisual(View content)
{
  const auto range = Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT;
  uint32_t count = content.GetVisualCount(range);

  if(count > 0u)
  {
    VisualBase visual = content.GetVisualAt(range, count - 1u);
    content.RemoveVisual(visual);
  }
}
```

## Image Visuals

`Dali::Ui::ImageVisual` renders a still image resource. Its property owner is `Dali::Ui::ImageVisual::Property`, which includes `URL`, `DESIRED_WIDTH`, `DESIRED_HEIGHT`, `FITTING_MODE`, `PIXEL_AREA`, `ALPHA_MASK_URL`, `MASKING_TYPE`, `CROP_TO_MASK`, `SYNCHRONOUS_LOADING`, `SYNCHRONOUS_SIZING`, `ENABLE_BROKEN_IMAGE`, `LOAD_POLICY`, `RELEASE_POLICY`, `SAMPLING_MODE`, `WRAP_MODE_U`, `WRAP_MODE_V`, `PRE_MULTIPLIED_ALPHA`, `FAST_TRACK_UPLOADING`, and n-patch properties such as `BORDER`, `BORDER_ONLY`, `AUXILIARY_IMAGE`, and `AUXILIARY_IMAGE_ALPHA`.

Use image visuals for decorative imagery inside an existing view, especially when the image is one layer of a larger control.

```cpp
void AddThumbnail(View tile)
{
  ImageVisual thumbnail = ImageVisual::New()
                            .SetResourceUrl("/opt/usr/apps/example/res/thumb.jpg")
                            .SetDesiredWidth(256)
                            .SetDesiredHeight(144)
                            .SetEnableBrokenImage(true);

  tile.AddVisual(thumbnail, Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT);
}
```

For partial image rendering, configure the source area through `Dali::Ui::ImageVisual::Property::PIXEL_AREA` or `Dali::Ui::ImageVisual::SetPixelArea`. The value is a normalized `Dali::Vector4` in `(x, y, width, height)` form, where the default `[0.0, 0.0, 1.0, 1.0]` uses the entire image.

```cpp
void AddCroppedImage(View tile)
{
  ImageVisual cropped = ImageVisual::New()
                          .SetResourceUrl("/opt/usr/apps/example/res/sprites.png")
                          .SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f))
                          .SetDesiredWidth(128)
                          .SetDesiredHeight(128);

  tile.AddVisual(cropped, Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT);
}
```

## Animated Image Visuals

`Dali::Ui::AnimatedImageVisual` is the visual type for animated image content. It supports playback control through `Play`, `Pause`, `Stop`, `JumpTo`, and state queries such as `GetPlayState`, `GetCurrentFrameNumber`, and `GetTotalFrameNumber`.

Use `Dali::Ui::AnimatedImageVisual::New` to create the visual, then configure playback and loading behavior with typed setters such as `SetResourceUrl`, `SetResourceUrlList`, `SetFrameSpeedFactor`, `SetLoopCount`, `SetCacheSize`, and `SetBatchSize`.

```cpp
AnimatedImageVisual CreateLoadingVisual()
{
  return AnimatedImageVisual::New()
           .SetResourceUrl("/opt/usr/apps/example/res/loading.gif")
           .SetDesiredWidth(96)
           .SetDesiredHeight(96)
           .SetFrameSpeedFactor(1.0f)
           .SetLoopCount(0)
           .SetCacheSize(4)
           .SetBatchSize(2)
           .SetEnableBrokenImage(true)
           .Play();
}
```

For an image sequence, use a URL list rather than a single animated image file. `SetFrameDelay` controls the delay between frames for the URL-list sequence.

```cpp
AnimatedImageVisual CreateSequenceVisual()
{
  return AnimatedImageVisual::New()
           .SetResourceUrlList({
             "/opt/usr/apps/example/res/frame-001.png",
             "/opt/usr/apps/example/res/frame-002.png",
             "/opt/usr/apps/example/res/frame-003.png",
           })
           .SetFrameDelay(120)
           .SetLoopCount(3)
           .Play();
}
```

You can keep the visual handle after setup and control playback from input handlers.

```cpp
class AnimatedBadge
{
public:
  void Build(View parent)
  {
    mVisual = AnimatedImageVisual::New()
                .SetResourceUrl("/opt/usr/apps/example/res/badge.webp")
                .SetLoopCount(0)
                .Play();

    parent.AddVisual(mVisual, Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT);
  }

  void Toggle()
  {
    if(mVisual.GetPlayState() == AnimatedImage::PlayState::PLAYING)
    {
      mVisual.Pause();
    }
    else
    {
      mVisual.Play();
    }
  }

  void ShowFirstFrame()
  {
    mVisual.JumpTo(0);
  }

private:
  AnimatedImageVisual mVisual;
};
```

The `Dali::Ui::AnimatedImageVisual::Property` namespace owns animation-specific properties such as `URL`, `PLAY_STATE`, `CURRENT_FRAME_NUMBER`, `TOTAL_FRAME_NUMBER`, `FRAME_DELAY`, `FRAME_SPEED_FACTOR`, `LOOP_COUNT`, `PLAY_RANGE`, `STOP_BEHAVIOR`, `CACHE_SIZE`, and `BATCH_SIZE`.

## Color Visuals

`Dali::Ui::ColorVisual` is useful for fill layers, overlays, shadows, and visual decoration on a `Dali::Ui::View`. Its property owner is `Dali::Ui::ColorVisual::Property`, which includes `BLUR_RADIUS` and `CUTOUT_POLICY`.

A common use is to add a color visual behind content, then use the view's layout and visual container ordering to position it relative to the content.

```cpp
void AddColorLayer(View panel)
{
  ColorVisual layer = ColorVisual::New()
                        .SetColor(UiColor(0x003070).WithAlpha(0.35f))
                        .SetBlurRadius(12_spx)
                        .SetCutoutPolicy(CutoutPolicy::CUTOUT_VIEW_WITH_CORNER_RADIUS);

  panel.AddVisual(layer, Visual::ContainerRangeType::BETWEEN_BACKGROUND_EFFECT_AND_BACKGROUND);
}
```

Color visuals also work well as additional foreground or background effects when combined with the view's corner and border styling.

```cpp
void AddPressedOverlay(View button)
{
  ColorVisual overlay = ColorVisual::New()
                          .SetColor(UiColor(0x000000).WithAlpha(0.12f))
                          .SetCutoutPolicy(CutoutPolicy::CUTOUT_OUTSIDE_WITH_CORNER_RADIUS);

  button.AddVisual(overlay, Visual::ContainerRangeType::BETWEEN_DECORATION_AND_FOREGROUND_EFFECT);
}
```

## Lottie Animation Visuals

`Dali::Ui::LottieAnimationVisual` represents Lottie animation content. Its property namespace, `Dali::Ui::LottieAnimationVisual::Property`, includes `URL`, `PLAY_STATE`, `CURRENT_FRAME_NUMBER`, `TOTAL_FRAME_NUMBER`, `PLAY_RANGE`, `FRAME_SPEED_FACTOR`, `LOOP_COUNT`, `LOOPING_MODE`, `RENDER_SCALE`, `ENABLE_FRAME_CACHE`, `MARKER_INFO`, `CONTENT_INFO`, and rasterization-related properties such as `NOTIFY_AFTER_RASTERIZATION`, `REDRAW_IN_SCALING_UP`, and `REDRAW_IN_SCALING_DOWN`.

Use Lottie visuals when the source is a Lottie JSON animation and you want it to live as a visual layer on a view.

```cpp
void AddLottieBadge(View parent)
{
  LottieAnimationVisual animation = LottieAnimationVisual::New()
                                      .SetResourceUrl("/opt/usr/apps/example/res/success.json")
                                      .SetDesiredWidth(160)
                                      .SetDesiredHeight(160)
                                      .SetFrameSpeedFactor(1.0f)
                                      .SetLoopCount(1)
                                      .Play();

  parent.AddVisual(animation, Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT);
}
```

For a subrange animation, pass a `Dali::Property::Array` to `SetPlayRange`.

```cpp
LottieAnimationVisual CreateIntroSegment()
{
  Property::Array range;
  range.PushBack(0);
  range.PushBack(45);

  return LottieAnimationVisual::New()
           .SetResourceUrl("/opt/usr/apps/example/res/intro.json")
           .SetPlayRange(range)
           .SetLoopCount(1)
           .Play();
}
```

## Inspecting Visual State

Use typed getters when the visual class provides them. `Dali::Ui::AnimatedImageVisual` exposes image setup, loading, masking, sizing, and playback state through getters such as `GetResourceUrl`, `GetResourceUrlList`, `GetDesiredWidth`, `GetDesiredHeight`, `GetFrameDelay`, `GetFrameSpeedFactor`, `GetLoopCount`, `GetCurrentFrameNumber`, `GetTotalFrameNumber`, and `GetPlayState`.

```cpp
void LogAnimatedImageState(const AnimatedImageVisual& visual)
{
  DALI_LOG_RELEASE_INFO(
    "url=%s frame=%d/%d delay=%d loop=%d",
    visual.GetResourceUrl().c_str(),
    visual.GetCurrentFrameNumber(),
    visual.GetTotalFrameNumber(),
    visual.GetFrameDelay(),
    visual.GetLoopCount());
}
```

Use boolean getters for optional behavior that affects rendering and loading, including `IsSynchronousLoading`, `IsSynchronousSizing`, `IsPreMultipliedAlpha`, `IsOrientationCorrection`, `IsBrokenImageEnabled`, and `IsCropToMask`.

```cpp
bool UsesSynchronousImageWork(const AnimatedImageVisual& visual)
{
  return visual.IsSynchronousLoading() || visual.IsSynchronousSizing();
}
```

For layout-sensitive animation, query the configured pixel and sizing data before deciding how to place the visual on a `Dali::Ui::View`.

```cpp
Vector4 GetAnimatedSourceArea(const AnimatedImageVisual& visual)
{
  return visual.GetPixelArea();
}
```
