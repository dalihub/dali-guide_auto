---
title: Visuals
sidebar_label: Visuals
category: images-visuals
---

# Visuals

Visuals are reusable `dali-ui` content objects that you attach to a `Dali::Ui::View` to draw color fills, images, animated image sequences, and Lottie animations.

## Table of Contents

- [Attach Visuals to a View](#attach-visuals-to-a-view)
- [Draw Static Images](#draw-static-images)
- [Add Color Layers and Soft Effects](#add-color-layers-and-soft-effects)
- [Play Animated Image Sequences](#play-animated-image-sequences)
- [Use Lottie Animation Visuals](#use-lottie-animation-visuals)
- [Inspect and Reorder Visuals](#inspect-and-reorder-visuals)

## Attach Visuals to a View

A visual is app-facing content owned by a `Dali::Ui::View`. Create a concrete visual such as `Dali::Ui::ImageVisual`, `Dali::Ui::ColorVisual`, `Dali::Ui::AnimatedImageVisual`, or `Dali::Ui::LottieAnimationVisual`, then add it to a view with `AddVisual`.

`Dali::Ui::Visual::ContainerRangeType` controls where the visual is drawn relative to the view's background effect, background, content, decoration, and foreground effect ranges.

```cpp
void AddPanelImage(Dali::Ui::View panel)
{
  Dali::Ui::ImageVisual visual =
    Dali::Ui::ImageVisual::New()
      .SetResourceUrl(Dali::String("/opt/usr/apps/org.example/res/panel.png"))
      .SetDesiredWidth(480)
      .SetDesiredHeight(270)
      .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO)
      .SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX_THEN_LINEAR)
      .SetEnableBrokenImage(true);

  panel.AddVisual(
    visual,
    Dali::Ui::Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT);
}
```

For declarative view construction, `Dali::Ui::View::Visuals` appends an initializer list of `Dali::Ui::VisualBase` objects to a container range. It asserts if a visual cannot be added, so use `AddVisual` when you need to handle duplicate or capacity errors explicitly.

```cpp
Dali::Ui::View MakeCard()
{
  return Dali::Ui::View::New()
    .Visuals(
      Dali::Ui::Visual::ContainerRangeType::BETWEEN_BACKGROUND_EFFECT_AND_BACKGROUND,
      {
        Dali::Ui::ColorVisual::New()
          .SetName(Dali::String("card-shadow"))
          .SetColor(Dali::Ui::UiColor(0x000000).WithAlpha(0.22f))
          .SetBlurRadius(18.0f)
          .SetExtraWidth(24.0f)
          .SetExtraHeight(24.0f)
      })
    .Visuals(
      Dali::Ui::Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT,
      {
        Dali::Ui::ImageVisual::New()
          .SetName(Dali::String("card-image"))
          .SetResourceUrl(Dali::String("/opt/usr/apps/org.example/res/card.png"))
          .SetFittingMode(Dali::Ui::Image::FittingMode::FILL)
      });
}
```

## Draw Static Images

Use `Dali::Ui::ImageVisual` for a single image resource. The `URL` is the required image input; typed setters such as `SetResourceUrl`, `SetDesiredWidth`, `SetDesiredHeight`, `SetFittingMode`, and `SetSamplingMode` configure common image loading and sampling behavior.

```cpp
Dali::Ui::ImageVisual MakeThumbnailVisual()
{
  return Dali::Ui::ImageVisual::New()
    .SetName(Dali::String("thumbnail"))
    .SetResourceUrl(Dali::String("/opt/usr/apps/org.example/res/thumb.jpg"))
    .SetDesiredWidth(160)
    .SetDesiredHeight(160)
    .SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO)
    .SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX)
    .SetLoadPolicy(Dali::Ui::Image::LoadPolicy::ATTACHED)
    .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DETACHED)
    .SetOrientationCorrection(true);
}
```

`Dali::Ui::ImageVisual::Property` owns the image-specific property constants, including `Dali::Ui::ImageVisual::Property::URL`, `Dali::Ui::ImageVisual::Property::PIXEL_AREA`, `Dali::Ui::ImageVisual::Property::ALPHA_MASK_URL`, and `Dali::Ui::ImageVisual::Property::BORDER`. Prefer typed setters for normal application code, and use property constants when a generic `Dali::Ui::VisualBase` path needs indexed access.

```cpp
void ConfigureImageThroughVisualBase(Dali::Ui::VisualBase visual)
{
  visual.SetProperty(
    Dali::Ui::ImageVisual::Property::PIXEL_AREA,
    Dali::Vector4(0.0f, 0.0f, 0.5f, 1.0f));

  visual.SetProperty(
    Dali::Ui::ImageVisual::Property::ALPHA_MASK_URL,
    Dali::String("/opt/usr/apps/org.example/res/mask.png"));
}
```

For scalable N-patch resources, `Dali::Ui::ImageVisual::Property::BORDER`, `Dali::Ui::ImageVisual::Property::BORDER_ONLY`, `Dali::Ui::ImageVisual::Property::AUXILIARY_IMAGE`, and `Dali::Ui::ImageVisual::Property::AUXILIARY_IMAGE_ALPHA` belong to `Dali::Ui::ImageVisual::Property`.

```cpp
Dali::Ui::ImageVisual MakeNinePatchBackground()
{
  return Dali::Ui::ImageVisual::New()
    .SetResourceUrl(Dali::String("/opt/usr/apps/org.example/res/panel.9.png"))
    .SetNPatchBorder(Dali::Extents(12u, 12u, 12u, 12u))
    .SetNPatchBorderOnly(false)
    .SetNPatchAuxiliaryImage(Dali::String("/opt/usr/apps/org.example/res/panel-overlay.png"))
    .SetNPatchAuxiliaryImageAlpha(0.35f);
}
```

## Add Color Layers and Soft Effects

Use `Dali::Ui::ColorVisual` for solid fills, overlays, soft shadows, and cutout effects attached to a `Dali::Ui::View`. `Dali::Ui::ColorVisual::Property` owns `Dali::Ui::ColorVisual::Property::BLUR_RADIUS` and `Dali::Ui::ColorVisual::Property::CUTOUT_POLICY`.

```cpp
void AddSoftScrim(Dali::Ui::View content)
{
  Dali::Ui::ColorVisual scrim =
    Dali::Ui::ColorVisual::New()
      .SetName(Dali::String("scrim"))
      .SetColor(Dali::Ui::UiColor(0x000000).WithAlpha(0.32f))
      .SetBlurRadius(0.0f)
      .SetWidth(1.0f)
      .SetHeight(1.0f)
      .SetProportionFlags(Dali::Ui::Visual::Transform::ProportionFlags::SIZE_PROPORTIONAL);

  content.AddVisual(
    scrim,
    Dali::Ui::Visual::ContainerRangeType::BETWEEN_DECORATION_AND_FOREGROUND_EFFECT);
}
```

For shadow or clipped overlay effects, combine `SetBlurRadius` with `SetCutoutPolicy`. The `CutoutPolicy` enum provides `CUTOUT_VIEW_WITH_CORNER_RADIUS` for the view area including its corner radius and `CUTOUT_OUTSIDE_WITH_CORNER_RADIUS` for the outside area including its corner radius.

```cpp
Dali::Ui::ColorVisual MakeInnerShadow()
{
  using Dali::Ui::CutoutPolicy;

  return Dali::Ui::ColorVisual::New()
    .SetName(Dali::String("inner-shadow"))
    .SetColor(Dali::Ui::UiColor(0x000000).WithAlpha(0.0f))
    .SetBlurRadius(32.0f)
    .SetBorderlineWidth(20.0f)
    .SetBorderlineColor(Dali::Ui::UiColor(0x000000).WithAlpha(0.45f))
    .SetBorderlineOffset(1.0f)
    .SetCornerRadius(18.0f)
    .SetCutoutPolicy(CutoutPolicy::CUTOUT_OUTSIDE_WITH_CORNER_RADIUS);
}
```

## Play Animated Image Sequences

Use `Dali::Ui::AnimatedImageVisual` for animated image resources or a sequence of image URLs. `SetResourceUrl` and `SetResourceUrlList` are mutually exclusive: use `SetResourceUrl` for a single resource and `SetResourceUrlList` for a frame sequence. `SetFrameDelay` applies to a sequence of multiple URLs.

```cpp
Dali::Ui::AnimatedImageVisual MakeLoadingSpinner()
{
  return Dali::Ui::AnimatedImageVisual::New()
    .SetResourceUrl(Dali::String("/opt/usr/apps/org.example/res/loading.webp"))
    .SetDesiredWidth(96)
    .SetDesiredHeight(96)
    .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO)
    .SetLoopCount(-1)
    .SetFrameSpeedFactor(1.0f)
    .SetEnableBrokenImage(true)
    .Play();
}
```

```cpp
Dali::Ui::AnimatedImageVisual MakeFrameSequence()
{
  return Dali::Ui::AnimatedImageVisual::New()
    .SetResourceUrlList({
      Dali::String("/opt/usr/apps/org.example/res/progress-00.png"),
      Dali::String("/opt/usr/apps/org.example/res/progress-01.png"),
      Dali::String("/opt/usr/apps/org.example/res/progress-02.png")
    })
    .SetFrameDelay(80)
    .SetBatchSize(2)
    .SetCacheSize(4)
    .SetLoopCount(3)
    .SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::LAST_FRAME)
    .Play();
}
```

`Dali::Ui::AnimatedImageVisual::Property` owns animation-specific property constants such as `Dali::Ui::AnimatedImageVisual::Property::FRAME_DELAY`, `Dali::Ui::AnimatedImageVisual::Property::BATCH_SIZE`, `Dali::Ui::AnimatedImageVisual::Property::CACHE_SIZE`, `Dali::Ui::AnimatedImageVisual::Property::LOOP_COUNT`, `Dali::Ui::AnimatedImageVisual::Property::PLAY_STATE`, `Dali::Ui::AnimatedImageVisual::Property::CURRENT_FRAME_NUMBER`, and `Dali::Ui::AnimatedImageVisual::Property::TOTAL_FRAME_NUMBER`.

```cpp
void PauseAndInspect(Dali::Ui::AnimatedImageVisual visual)
{
  visual.Pause();

  int currentFrame = visual.GetCurrentFrameNumber();
  int totalFrames = visual.GetTotalFrameNumber();
  Dali::Ui::AnimatedImage::PlayState state = visual.GetPlayState();

  if(totalFrames > 0 && state == Dali::Ui::AnimatedImage::PlayState::PAUSED)
  {
    int nextFrame = (currentFrame + 1) % totalFrames;
    visual.JumpTo(nextFrame);
  }
}
```

## Use Lottie Animation Visuals

Use `Dali::Ui::LottieAnimationVisual` for Lottie JSON animation files. It shares common image loading properties with `Dali::Ui::ImageVisual` and playback properties such as `SetLoopCount`, `SetPlayRange`, `SetStopBehavior`, and `SetFrameSpeedFactor`.

```cpp
Dali::Ui::LottieAnimationVisual MakeSuccessAnimation()
{
  return Dali::Ui::LottieAnimationVisual::New()
    .SetResourceUrl(Dali::String("/opt/usr/apps/org.example/res/success.json"))
    .SetDesiredWidth(240)
    .SetDesiredHeight(240)
    .SetLoopCount(1)
    .SetFrameSpeedFactor(1.0f)
    .SetStopBehavior(Dali::Ui::AnimatedImage::StopBehavior::LAST_FRAME)
    .SetEnableFrameCache(true)
    .SetRenderScale(1.0f)
    .Play();
}
```

`Dali::Ui::LottieAnimationVisual::Property` owns Lottie-specific constants such as `Dali::Ui::LottieAnimationVisual::Property::ENABLE_FRAME_CACHE`, `Dali::Ui::LottieAnimationVisual::Property::RENDER_SCALE`, `Dali::Ui::LottieAnimationVisual::Property::CONTENT_INFO`, and `Dali::Ui::LottieAnimationVisual::Property::MARKER_INFO`. `CONTENT_INFO` and `MARKER_INFO` are read-only lookup data, so retrieve them with `GetContentInfo`, `GetMarkerInfo`, or indexed property reads rather than setting them.

```cpp
void UseLottieMarkers(Dali::Ui::LottieAnimationVisual visual)
{
  Dali::Property::Map markerInfo = visual.GetMarkerInfo();
  Dali::Property::Map contentInfo = visual.GetContentInfo();

  visual.SetPlayRange(Dali::Property::Array()
    .Add(Dali::String("intro"))
    .Add(Dali::String("complete")));

  visual.Play();
}
```

For direct frame control, use `Play`, `Pause`, `Stop`, and `JumpTo` on `Dali::Ui::LottieAnimationVisual`.

```cpp
void RestartLottie(Dali::Ui::LottieAnimationVisual visual)
{
  visual.Stop()
        .JumpTo(0)
        .Play();
}
```

## Inspect and Reorder Visuals

Every concrete visual is a `Dali::Ui::VisualBase`. After a visual is attached, `GetOwner` returns the owning `Dali::Ui::View`, `GetContainerRangeType` returns the visual container, and `GetSiblingOrder` returns its order within that container.

```cpp
void MoveTopVisualToFront(Dali::Ui::View view)
{
  auto range = Dali::Ui::Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT;
  uint32_t count = view.GetVisualCount(range);

  if(count == 0u)
  {
    return;
  }

  Dali::Ui::VisualBase visual = view.GetVisualAt(range, count - 1u);
  visual.RaiseToTop();
}
```

A `Dali::Ui::VisualBase` can belong to only one view container at a time. Adding the same visual to another `Dali::Ui::View` or another container range moves it from the previous owner or range to the new one. Sibling-order APIs such as `RaiseToTop`, `LowerToBottom`, `RaiseAbove`, and `LowerBelow` reorder visuals only within the same container range. To remove a visual explicitly, call `RemoveVisual` on the owning view or call `Detach` on the visual.

```cpp
void MoveVisual(Dali::Ui::View source, Dali::Ui::View target)
{
  auto range = Dali::Ui::Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT;

  if(source.GetVisualCount(range) == 0u)
  {
    return;
  }

  Dali::Ui::VisualBase visual = source.GetVisualAt(range, 0u);

  target.AddVisual(
    visual,
    Dali::Ui::Visual::ContainerRangeType::BETWEEN_DECORATION_AND_FOREGROUND_EFFECT);
}
```
