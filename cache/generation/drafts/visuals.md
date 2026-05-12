---
title: Visuals
sidebar_label: Visuals
category: images-visuals
---

# Visuals

`dali-ui` visuals describe image, color, and animated content that an application composes onto a `Dali::Ui::View`.

## Table of Contents

- [Choosing a Visual Type](#choosing-a-visual-type)
- [Configuring Animated Image Visuals](#configuring-animated-image-visuals)
- [Masking and Cropping](#masking-and-cropping)
- [Playback Control and Frame State](#playback-control-and-frame-state)
- [Property Ownership](#property-ownership)

## Choosing a Visual Type

Use `Dali::Ui::View` as the application-facing object in your view tree, and use visual objects to describe what that view should render. The main visual types for image and color content are:

- `Dali::Ui::ImageVisual` for still image content.
- `Dali::Ui::AnimatedImageVisual` for animated image content or an image sequence.
- `Dali::Ui::ColorVisual` for simple color content and visual effects such as blur/cutout.
- `Dali::Ui::LottieAnimationVisual` for Lottie animation content.

Each visual is created through its typed `New()` constructor. Prefer typed setters when the visual exposes them, because they preserve the fluent `dali-ui` style and avoid raw property plumbing in application code.

```cpp
Dali::Ui::ImageVisual imageVisual = Dali::Ui::ImageVisual::New();

Dali::Ui::AnimatedImageVisual animatedVisual =
  Dali::Ui::AnimatedImageVisual::New()
    .SetDesiredWidth(240)
    .SetDesiredHeight(160)
    .SetEnableBrokenImage(true);

Dali::Ui::ColorVisual colorVisual = Dali::Ui::ColorVisual::New();

Dali::Ui::LottieAnimationVisual lottieVisual = Dali::Ui::LottieAnimationVisual::New();
```

`Dali::Ui::VisualBase` is the common visual handle type. Application code normally keeps the concrete type, such as `Dali::Ui::ImageVisual` or `Dali::Ui::AnimatedImageVisual`, while configuring feature-specific properties.

## Configuring Animated Image Visuals

`Dali::Ui::AnimatedImageVisual` owns the animated-image visual behavior. Its typed setters return `Dali::Ui::AnimatedImageVisual&`, so configuration can be chained before the visual is used by a `Dali::Ui::View`.

Use `SetDesiredWidth()` and `SetDesiredHeight()` to request decode or sizing dimensions. Use `SetFittingMode()` for how image content fits the visual area, and enable broken-image fallback with `SetEnableBrokenImage()` when the UI should still render a fallback if loading fails.

```cpp
Dali::Ui::AnimatedImageVisual visual =
  Dali::Ui::AnimatedImageVisual::New()
    .SetDesiredWidth(320)
    .SetDesiredHeight(180)
    .SetFittingMode(Dali::Image::FittingMode::SCALE_TO_FILL)
    .SetEnableBrokenImage(true);
```

For loading and memory behavior, `SetLoadPolicy()` and `SetReleasePolicy()` configure when image resources are loaded and released. `SetSynchronousLoading()` and `SetSynchronousSizing()` are available when the app needs synchronous behavior.

```cpp
Dali::Ui::AnimatedImageVisual visual =
  Dali::Ui::AnimatedImageVisual::New()
    .SetLoadPolicy(Dali::Image::LoadPolicy::IMMEDIATE)
    .SetReleasePolicy(Dali::Image::ReleasePolicy::DETACHED)
    .SetSynchronousLoading(false)
    .SetSynchronousSizing(false);
```

For animation throughput, use `SetBatchSize()` and `SetCacheSize()` to tune frame preparation and caching. `SetFrameDelay()` sets the delay between frames for image-sequence animation, and `SetFrameSpeedFactor()` scales playback speed.

```cpp
Dali::Ui::AnimatedImageVisual visual =
  Dali::Ui::AnimatedImageVisual::New()
    .SetBatchSize(2)
    .SetCacheSize(4)
    .SetFrameDelay(100)
    .SetFrameSpeedFactor(1.0f)
    .SetLoopCount(0);
```

The matching getters, such as `GetDesiredWidth()`, `GetDesiredHeight()`, `GetBatchSize()`, `GetCacheSize()`, `GetFrameDelay()`, `GetFrameSpeedFactor()`, and `GetLoopCount()`, let application code inspect the configured values.

```cpp
Dali::Ui::AnimatedImageVisual visual =
  Dali::Ui::AnimatedImageVisual::New()
    .SetDesiredWidth(128)
    .SetDesiredHeight(128)
    .SetFrameDelay(80);

const int width      = visual.GetDesiredWidth();
const int height     = visual.GetDesiredHeight();
const int frameDelay = visual.GetFrameDelay();
```

## Masking and Cropping

`Dali::Ui::AnimatedImageVisual` supports alpha masking through `SetAlphaMaskUrl()`. Use `SetMaskContentScale()` to control mask content scaling and `SetCropToMask()` when content should be cropped to the mask.

```cpp
Dali::Ui::AnimatedImageVisual visual =
  Dali::Ui::AnimatedImageVisual::New()
    .SetAlphaMaskUrl("images/avatar-mask.png")
    .SetMaskContentScale(1.0f)
    .SetCropToMask(true);
```

Use `SetMaskingType()` when the app needs to choose the masking path explicitly.

```cpp
Dali::Ui::AnimatedImageVisual visual =
  Dali::Ui::AnimatedImageVisual::New()
    .SetAlphaMaskUrl("images/card-mask.png")
    .SetMaskingType(Dali::Image::MaskingType::MASKING_ON_RENDERING);
```

`GetAlphaMaskUrl()`, `GetMaskContentScale()`, `IsCropToMask()`, and `GetMaskingType()` return the currently configured masking values.

```cpp
const Dali::String maskUrl = visual.GetAlphaMaskUrl();
const float maskScale      = visual.GetMaskContentScale();
const bool cropToMask      = visual.IsCropToMask();
```

For displaying a sub-region of image content, use `SetPixelArea()` with a `Dali::Vector4`. The same value can be read back with `GetPixelArea()`.

```cpp
Dali::Ui::AnimatedImageVisual visual =
  Dali::Ui::AnimatedImageVisual::New()
    .SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.5f, 0.5f));

const Dali::Vector4 area = visual.GetPixelArea();
```

## Playback Control and Frame State

`Dali::Ui::AnimatedImageVisual` owns playback control for animated image visuals. `Play()`, `Pause()`, and `JumpTo()` return the visual reference, so they can be used directly or in a fluent chain.

```cpp
Dali::Ui::AnimatedImageVisual visual =
  Dali::Ui::AnimatedImageVisual::New()
    .SetLoopCount(0)
    .SetFrameSpeedFactor(1.0f)
    .Play();

visual.Pause();
visual.JumpTo(5).Play();
```

Use `GetPlayState()`, `GetCurrentFrameNumber()`, and `GetTotalFrameNumber()` to inspect playback state. `GetTotalFrameNumber()` can report that decoding has not completed yet, so frame-dependent UI should tolerate a not-yet-ready value.

```cpp
const Dali::AnimatedImage::PlayState state = visual.GetPlayState();
const int currentFrame                     = visual.GetCurrentFrameNumber();
const int totalFrames                      = visual.GetTotalFrameNumber();
```

`SetLoopCount()` controls repeat count. `SetPlayRange()` uses a `Dali::Property::Array` to describe the playable frame range, and `GetPlayRange()` returns the configured range.

```cpp
Dali::Property::Array playRange;
playRange.PushBack(4);
playRange.PushBack(18);

Dali::Ui::AnimatedImageVisual visual =
  Dali::Ui::AnimatedImageVisual::New()
    .SetLoopCount(3)
    .SetPlayRange(playRange)
    .Play();

const Dali::Property::Array configuredRange = visual.GetPlayRange();
```

## Property Ownership

Visual property constants are owned by the visual type that exposes them. Use the owner type in documentation, diagnostics, and any property-table code so the property meaning remains clear.

`Dali::Ui::ImageVisual::Property` owns still-image properties such as `Dali::Ui::ImageVisual::Property::URL`, `Dali::Ui::ImageVisual::Property::PIXEL_AREA`, `Dali::Ui::ImageVisual::Property::FITTING_MODE`, `Dali::Ui::ImageVisual::Property::ALPHA_MASK_URL`, `Dali::Ui::ImageVisual::Property::BORDER`, and `Dali::Ui::ImageVisual::Property::AUXILIARY_IMAGE`.

```cpp
const int imageUrlProperty       = Dali::Ui::ImageVisual::Property::URL;
const int imagePixelAreaProperty = Dali::Ui::ImageVisual::Property::PIXEL_AREA;
const int imageMaskProperty      = Dali::Ui::ImageVisual::Property::ALPHA_MASK_URL;
```

`Dali::Ui::AnimatedImageVisual::Property` owns animated-image properties such as `Dali::Ui::AnimatedImageVisual::Property::URL`, `Dali::Ui::AnimatedImageVisual::Property::FRAME_DELAY`, `Dali::Ui::AnimatedImageVisual::Property::FRAME_SPEED_FACTOR`, `Dali::Ui::AnimatedImageVisual::Property::LOOP_COUNT`, `Dali::Ui::AnimatedImageVisual::Property::PLAY_STATE`, `Dali::Ui::AnimatedImageVisual::Property::CURRENT_FRAME_NUMBER`, and `Dali::Ui::AnimatedImageVisual::Property::TOTAL_FRAME_NUMBER`.

```cpp
const int animatedUrlProperty   = Dali::Ui::AnimatedImageVisual::Property::URL;
const int frameDelayProperty    = Dali::Ui::AnimatedImageVisual::Property::FRAME_DELAY;
const int currentFrameProperty  = Dali::Ui::AnimatedImageVisual::Property::CURRENT_FRAME_NUMBER;
const int totalFrameProperty    = Dali::Ui::AnimatedImageVisual::Property::TOTAL_FRAME_NUMBER;
```

`Dali::Ui::ColorVisual::Property` owns color-visual effect properties: `Dali::Ui::ColorVisual::Property::BLUR_RADIUS` and `Dali::Ui::ColorVisual::Property::CUTOUT_POLICY`.

```cpp
const int blurRadiusProperty  = Dali::Ui::ColorVisual::Property::BLUR_RADIUS;
const int cutoutPolicyProperty = Dali::Ui::ColorVisual::Property::CUTOUT_POLICY;
```

`Dali::Ui::LottieAnimationVisual::Property` owns Lottie-specific animation properties such as `Dali::Ui::LottieAnimationVisual::Property::URL`, `Dali::Ui::LottieAnimationVisual::Property::LOOPING_MODE`, `Dali::Ui::LottieAnimationVisual::Property::RENDER_SCALE`, `Dali::Ui::LottieAnimationVisual::Property::CONTENT_INFO`, and `Dali::Ui::LottieAnimationVisual::Property::MARKER_INFO`.

```cpp
const int lottieUrlProperty       = Dali::Ui::LottieAnimationVisual::Property::URL;
const int lottieRenderScale       = Dali::Ui::LottieAnimationVisual::Property::RENDER_SCALE;
const int lottieContentInfo       = Dali::Ui::LottieAnimationVisual::Property::CONTENT_INFO;
const int lottieMarkerInfo        = Dali::Ui::LottieAnimationVisual::Property::MARKER_INFO;
```

For application code, keep the main composition model centered on `Dali::Ui::View`, then attach or manage these visual objects through the `dali-ui` view visual system used by the surrounding UI. This keeps rendering content separate from the app-facing view tree and avoids raw `Dali::Actor`-style object management.
