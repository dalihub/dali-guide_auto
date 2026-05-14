# Image Prose Review

## Summary

Reviewed the Image feature guide against public headers (`image-view.h`, `animated-image-view.h`, `image-enumerations.h`) and sample files. The draft was largely accurate with one clarification needed in the overview section.

## Changes Made

### 1. Overview Section (Clarification)

**Original:**
> ImageView displays static and animated image resources with controls for fitting, scaling, masking, and loading behavior.

**Revised:**
> `ImageView` displays image resources with controls for fitting, scaling, masking, and loading behavior. For animated images with playback control, use `AnimatedImageView`.

**Reason:** The original wording could mislead readers into thinking `ImageView` provides animated image playback. While `ImageView` can load animated image formats (GIF, WebP), it displays them statically. `AnimatedImageView` is the dedicated class for playback control. Added backticks around `ImageView` and `AnimatedImageView` per style requirements.

### 2. Added Default Fitting Mode Documentation

**Addition:**
Added "The default fitting mode is `FILL` (stretch to fill)." to the Fitting and Scaling section.

**Source Evidence:** `image-view.h` line 238: "The default fitting mode is Ui::Image::FittingMode::FILL (stretch to fill)."

### 3. Animated Images Section (Clarification)

**Original:**
> For animated GIF images, use `AnimatedImageView` which provides playback control:

**Revised:**
> For animated GIF and WebP images, use `AnimatedImageView` which provides playback control:

**Reason:** `AnimatedImageView` supports both GIF and animated WebP formats, as confirmed by sample file `animated-image-view-example.cpp` which demonstrates `dog-anim.webp` (WebP) and GIF files.

## Verified Accurate (No Changes Required)

- **Creating an ImageView**: `New()`, `SetResourceUrl()`, `ResourceReadySignal()`, `DownCast()`, `GetLoadingStatus()` - all verified against `image-view.h`
- **AnimatedImageView API**: `Play()`, `Pause()`, `Stop()`, `SetLoopCount()`, `GetCurrentFrame()`, `GetTotalFrame()`, `GetPlayState()`, `AnimationFinishedSignal()`, `SetResourceUrls()` - all verified against `animated-image-view.h`
- **Fitting and Scaling**: `SetFittingMode()`, `SetSamplingMode()`, `SetDesiredWidth()`, `SetDesiredHeight()` - enum values verified against `image-enumerations.h`
- **Image Region Display**: `SetPixelArea()`, `PIXEL_AREA` property index - verified against `image-view.h`
- **Color and Tinting**: `SetImageColor()` with `UiColor` - verified against both headers
- **Loading Behavior**: `SetLoadPolicy()`, `SetReleasePolicy()`, `SetSynchronousLoading()`, `SetFastTrackUpload()`, `Reload()` - all verified against `image-view.h`
- **Alpha Masking**: `SetAlphaMaskUrl()`, `SetCropToMask()`, `SetMaskingMode()` - verified against both headers
- **Code blocks**: All code examples use correct API signatures and fluent chaining idiom

## Remaining Considerations

1. **Animation example uses dali-core APIs**: The `PIXEL_AREA` animation example uses `Animation`, `KeyFrames`, `Property`, and `AlphaFunction` from dali-core. This is acceptable since animating view properties requires these APIs and they are well-established dali-core patterns.

2. **No LottieAnimationView coverage**: The guide focuses on `ImageView` and `AnimatedImageView`. `LottieAnimationView` is mentioned in sample files but is outside the scope of this Image feature document.

## Source Files Reviewed

- `repos/dali-ui/dali-ui-foundation/public-api/image-view.h`
- `repos/dali-ui/dali-ui-foundation/public-api/animated-image-view.h`
- `repos/dali-ui/dali-ui-foundation/public-api/image/image-enumerations.h`
- `repos/dali-ui/samples/image-view/image-fitting-mode-example.cpp`
- `repos/dali-ui/samples/image-view/image-loading-policy-example.cpp`
- `repos/dali-ui/samples/image-view/animated-image-view-example.cpp`
- `repos/dali-ui/samples/image-view/image-alpha-mask-example.cpp`
