-----BEGIN_REVISED_MARKDOWN-----
---
title: Image
sidebar_label: Image
category: uncategorized
---

# Image

`ImageView` and `AnimatedImageView` provide high-level components for displaying static and animated images in dali-ui applications.

## Table of Contents

- [Creating an ImageView](#creating-an-imageview)
- [Loading and Resource Management](#loading-and-resource-management)
- [Fitting and Scaling](#fitting-and-scaling)
- [Image Color and Tinting](#image-color-and-tinting)
- [Pixel Area and Sub-Regions](#pixel-area-and-sub-regions)
- [Animated Images](#animated-images)
- [Alpha Masking](#alpha-masking)

## Creating an ImageView

`ImageView` displays static image resources such as PNG, JPG, and other supported formats. Create an ImageView by passing a URL to the `New()` factory method:

```cpp
#include <dali-ui-foundation/public-api/image-view.h>

using namespace Dali::Ui;

// Create from a resource URL
ImageView imageView = ImageView::New("images/photo.jpg");

// Set size and add to the view tree
imageView.SetRequestedWidth(MATCH_PARENT)
          .SetRequestedHeight(MATCH_PARENT);

container.Add(imageView);
```

You can also create an empty ImageView and set the resource URL later:

```cpp
ImageView imageView = ImageView::New();
imageView.SetResourceUrl("images/photo.png");
```

Use `Reload()` to reload the current image from its source:

```cpp
imageView.Reload();
```

## Loading and Resource Management

### Asynchronous Loading

Images load asynchronously by default. Connect to `ResourceReadySignal()` to be notified when loading completes:

```cpp
class MyController : public ConnectionTracker
{
public:
    void SetupImage()
    {
        mImageView = ImageView::New("images/large.png");
        mImageView.ResourceReadySignal().Connect(this, &MyController::OnImageReady);
        container.Add(mImageView);
    }

    void OnImageReady(View view)
    {
        // Image is now displayed
        auto status = ImageView::DownCast(view).GetLoadingStatus();
    }

private:
    ImageView mImageView;
};
```

### Load Policy

Control when the image loads using `SetLoadPolicy()`:

- `Image::LoadPolicy::IMMEDIATE` — Load when the ImageView is created
- `Image::LoadPolicy::ATTACHED` — Load when the ImageView is attached to the scene (default)

```cpp
imageView.SetLoadPolicy(Image::LoadPolicy::IMMEDIATE);
```

### Release Policy

Control when the image texture is released from cache using `SetReleasePolicy()`:

- `Image::ReleasePolicy::DETACHED` — Release when removed from scene (default)
- `Image::ReleasePolicy::DESTROYED` — Release when the view is destroyed
- `Image::ReleasePolicy::NEVER` — Keep in cache indefinitely

```cpp
imageView.SetReleasePolicy(Image::ReleasePolicy::DETACHED);
```

### Synchronous Loading

For cases where you need the image immediately, enable synchronous loading:

```cpp
imageView.SetSynchronousLoading(true);
```

### Fast-Track Uploading

Enable background GPU upload to reduce main-thread stalls:

```cpp
imageView.SetFastTrackUpload(true);
```

### Placeholder Images

Set a placeholder image to display while the main image loads:

```cpp
imageView.SetPlaceholderUrl("images/placeholder.png");
```

## Fitting and Scaling

### Fitting Mode

Control how the image fits within the view bounds using `SetFittingMode()`. The default fitting mode is `FILL` (stretch to fill):

```cpp
// Scale to fit while preserving aspect ratio (may show letterboxing)
imageView.SetFittingMode(Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

// Stretch to fill the entire view (may distort)
imageView.SetFittingMode(Image::FittingMode::FILL);

// Scale to cover while preserving aspect ratio (may crop)
imageView.SetFittingMode(Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);

// Display at original image size, centered
imageView.SetFittingMode(Image::FittingMode::CENTER);
```

### Sampling Mode

Control the filter applied when scaling the image:

```cpp
// Nearest-neighbor for pixel art
imageView.SetSamplingMode(Image::SamplingMode::NEAREST);

// Linear filtering for smooth scaling
imageView.SetSamplingMode(Image::SamplingMode::LINEAR);

// High-quality Lanczos filtering
imageView.SetSamplingMode(Image::SamplingMode::LANCZOS);
```

Additional sampling modes include `BOX`, `BOX_THEN_NEAREST`, `BOX_THEN_LINEAR`, and `BOX_THEN_LANCZOS` for various quality/performance trade-offs.

### Desired Size Hints

Provide size hints to the image loader for memory efficiency:

```cpp
imageView.SetDesiredWidth(320);
imageView.SetDesiredHeight(480);
```

Alternatively, use `SetImageLoadWithViewSize()` to automatically load at the resolved view size:

```cpp
imageView.SetImageLoadWithViewSize(true);
```

## Image Color and Tinting

Apply a color multiplier to tint the image using `SetImageColor()`. White (1.0, 1.0, 1.0, 1.0) displays the original image:

```cpp
// No tint (original colors)
imageView.SetImageColor(UiColor(1.0f, 1.0f, 1.0f, 1.0f));

// Red tint
imageView.SetImageColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f));

// Grayscale effect
imageView.SetImageColor(UiColor(0.5f, 0.5f, 0.5f, 1.0f));

// 50% transparent
imageView.SetImageColor(UiColor(1.0f, 1.0f, 1.0f, 0.5f));
```

You can also use hex color values:

```cpp
imageView.SetImageColor(UiColor(0xFF0000));  // Red tint
```

## Pixel Area and Sub-Regions

Display a sub-region of the image using `SetPixelArea()`. The area is specified as normalized coordinates (x, y, width, height) in the range [0, 1]:

```cpp
// Display the full image
imageView.SetPixelArea(Vector4(0.0f, 0.0f, 1.0f, 1.0f));

// Display the top-left quadrant
imageView.SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f));

// Display the center region
imageView.SetPixelArea(Vector4(0.25f, 0.25f, 0.5f, 0.5f));
```

PixelArea is animatable, enabling pan and zoom effects:

```cpp
#include <dali/public-api/animation/animation.h>
#include <dali/public-api/animation/key-frames.h>

// Create key frames for a pan + zoom animation
KeyFrames keyFrames = KeyFrames::New();
keyFrames.Add(0.00f, Property::Value(Vector4(0.0f,  0.0f,  1.0f,  1.0f)));
keyFrames.Add(0.25f, Property::Value(Vector4(0.0f,  0.0f,  0.5f,  0.5f)));
keyFrames.Add(0.50f, Property::Value(Vector4(0.25f, 0.25f, 0.5f,  0.5f)));
keyFrames.Add(0.75f, Property::Value(Vector4(0.5f,  0.5f,  0.5f,  0.5f)));
keyFrames.Add(1.00f, Property::Value(Vector4(0.0f,  0.0f,  1.0f,  1.0f)));

Animation animation = Animation::New(3.0f);
animation.SetLooping(true);
animation.AnimateBetween(
    Property(imageView, ImageView::Property::PIXEL_AREA),
    keyFrames,
    AlphaFunction::EASE_IN_OUT);
animation.Play();
```

## Animated Images

`AnimatedImageView` displays animated image formats such as GIF and animated WebP. It also supports frame-by-frame animation from an array of image URLs. It provides playback control methods:

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

// Create from a GIF file
AnimatedImageView animView = AnimatedImageView::New("animations/spinner.gif");
animView.SetLoopCount(-1);  // Infinite looping
animView.Play();
```

### Playback Control

```cpp
// Start or resume playback
animView.Play();

// Pause playback
animView.Pause();

// Stop and reset to first frame
animView.Stop();
```

### Loop Configuration

```cpp
// Infinite looping
animView.SetLoopCount(-1);

// Play exactly 3 times
animView.SetLoopCount(3);
```

### Frame Control

```cpp
// Jump to a specific frame
animView.JumpToFrame(10);

// Get current frame
int currentFrame = animView.GetCurrentFrame();

// Get total frame count
int totalFrames = animView.GetTotalFrame();

// Adjust playback speed (values > 1 speed up, < 1 slow down)
animView.SetFrameSpeedFactor(1.5f);
```

### Animation Finished Signal

Connect to `AnimationFinishedSignal()` to be notified when the animation completes all loops:

```cpp
animView.AnimationFinishedSignal().Connect(this, &MyController::OnAnimationFinished);
```

### Frame Sequence from URL Array

You can also create animations from a sequence of individual image files:

```cpp
Dali::Vector<Dali::String> urls;
urls.PushBack(Dali::String("frame001.png"));
urls.PushBack(Dali::String("frame002.png"));
urls.PushBack(Dali::String("frame003.png"));

animView.SetResourceUrls(urls);
animView.SetBatchSize(4);       // Pre-load frames in batches
animView.SetCacheSize(10);      // Cache frames for smooth playback
animView.SetFrameDelay(100);    // Override frame delay in milliseconds
animView.Play();
```

### Common Image Properties

AnimatedImageView supports the same fitting, scaling, and color properties as ImageView:

```cpp
animView.SetFittingMode(Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
animView.SetImageColor(UiColor(0xFF8080));
animView.SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f));
```

## Alpha Masking

Apply an alpha mask to shape the visible region of the image:

```cpp
// Set the mask image URL
imageView.SetAlphaMaskUrl("masks/rounded-corner-mask.png");

// Optionally crop the image to mask bounds
imageView.SetCropToMask(true);

// Set masking mode
imageView.SetMaskingMode(Image::MaskingType::MASKING_ON_RENDERING);
```

The masking mode determines when the mask is applied:

- `Image::MaskingType::MASKING_ON_RENDERING` — Mask is applied during rendering
- `Image::MaskingType::MASKING_ON_LOADING` — Mask is baked into the texture at load time

### N-Patch Borders

For stretchable UI elements like buttons and frames, use N-patch borders. This feature is available on `ImageView`:

```cpp
// Set border insets (left, top, right, bottom)
imageView.SetNPatchBorder(Vector4(10, 10, 10, 10));

// Render only the border (for hollow frames)
imageView.SetNPatchBorderOnly(true);
```
-----END_REVISED_MARKDOWN-----

-----BEGIN_PROSE_REVIEW_REPORT-----
# Image Prose Review

## Summary

Reviewed the Image feature guide against public API headers (`image-view.h`, `animated-image-view.h`, `image-enumerations.h`) and sample files (`image-loading-policy-example.cpp`, `animated-image-view-example.cpp`). The draft was generally accurate with a few areas requiring clarification or correction.

## Changes Made

### 1. Overview Section
- **Change**: Added backticks around `ImageView` and `AnimatedImageView` in the opening paragraph for consistency with API naming conventions.
- **Source**: `image-view.h`, `animated-image-view.h`

### 2. Release Policy Section
- **Change**: Added "(default)" annotation to `Image::ReleasePolicy::DETACHED` to clarify the default behavior.
- **Source**: `image-enumerations.h` line 95: `DETACHED = 0` indicates it is the first/default value.

### 3. Fitting Mode Section
- **Change**: Added sentence stating "The default fitting mode is `FILL` (stretch to fill)" to clarify default behavior.
- **Source**: `image-view.h` docstring for `SetFittingMode()`: "The default fitting mode is Ui::Image::FittingMode::FILL (stretch to fill)."

### 4. Sampling Mode Section
- **Change**: Added sentence about additional sampling modes: "Additional sampling modes include `BOX`, `BOX_THEN_NEAREST`, `BOX_THEN_LINEAR`, and `BOX_THEN_LANCZOS` for various quality/performance trade-offs."
- **Source**: `image-enumerations.h` lines 56-69 listing all `SamplingMode` enum values.

### 5. Animated Images Section
- **Change**: Expanded the description to mention animated WebP support and frame-by-frame animation from URL arrays: "`AnimatedImageView` displays animated image formats such as GIF and animated WebP. It also supports frame-by-frame animation from an array of image URLs."
- **Source**: `animated-image-view-example.cpp` shows WebP support (line 236: "dog-anim.webp") and URL array usage via `SetResourceUrls()`.

### 6. Frame Sequence from URL Array (New Subsection)
- **Change**: Added new subsection demonstrating `SetResourceUrls()`, `SetBatchSize()`, `SetCacheSize()`, and `SetFrameDelay()` methods.
- **Source**: `animated-image-view.h` lines 108-135 (`SetResourceUrls`, `SetBatchSize`, `SetCacheSize`, `SetFrameDelay`); `animated-image-view-example.cpp` lines 197-213.

### 7. Alpha Masking Section
- **Change**: Added explanation of the two masking modes:
  - `Image::MaskingType::MASKING_ON_RENDERING` — Mask is applied during rendering
  - `Image::MaskingType::MASKING_ON_LOADING` — Mask is baked into the texture at load time
- **Source**: `image-enumerations.h` lines 47-52.

### 8. N-Patch Borders Section
- **Change**: Added clarification that N-patch borders are available on `ImageView`: "This feature is available on `ImageView`:"
- **Source**: `image-view.h` shows `SetNPatchBorder()` and `SetNPatchBorderOnly()` methods; `animated-image-view.h` does not have these methods.

## Verified Accurate (No Changes Needed)

- **Creating an ImageView**: `New()` factory methods, `SetResourceUrl()`, and `Reload()` are correctly documented.
- **Asynchronous Loading**: `ResourceReadySignal()` connection pattern is correct.
- **Load Policy**: Both `IMMEDIATE` and `ATTACHED` values correctly documented with `ATTACHED` as default.
- **Synchronous Loading**: `SetSynchronousLoading()` correctly documented.
- **Fast-Track Uploading**: `SetFastTrackUpload()` correctly documented.
- **Placeholder Images**: `SetPlaceholderUrl()` correctly documented.
- **Desired Size Hints**: `SetDesiredWidth()`, `SetDesiredHeight()`, and `SetImageLoadWithViewSize()` correctly documented.
- **Image Color and Tinting**: `SetImageColor()` with `UiColor` correctly documented.
- **Pixel Area and Sub-Regions**: `SetPixelArea()` with normalized coordinates and animation example are correct.
- **Playback Control**: `Play()`, `Pause()`, `Stop()` correctly documented.
- **Loop Configuration**: `SetLoopCount()` with -1 for infinite correctly documented.
- **Frame Control**: `JumpToFrame()`, `GetCurrentFrame()`, `GetTotalFrame()`, `SetFrameSpeedFactor()` correctly documented.
- **Animation Finished Signal**: `AnimationFinishedSignal()` correctly documented.
- **Common Image Properties**: Statement that AnimatedImageView supports same fitting, scaling, and color properties is verified accurate.

## Remaining Concerns

1. **LottieAnimationView**: The sample files reference `LottieAnimationView` which is related but not documented in this guide. Consider whether a cross-reference or separate guide is needed.

2. **Property-based API**: The samples show property-based usage (e.g., `view.SetProperty(ImageView::Property::FITTING_MODE, ...)`). The guide correctly uses typed setters which is the preferred idiom for app-facing code.

3. **GetPlayState()**: The `AnimatedImageView` API includes `GetPlayState()` which returns the current `PlayState`. This could be mentioned for completeness but is not essential for basic usage.

## Code Block Verification

All code blocks were verified against public headers:
- Include paths match actual header locations
- Method names and signatures are accurate
- Enum values match `Image::FittingMode`, `Image::SamplingMode`, `Image::LoadPolicy`, `Image::ReleasePolicy`, and `Image::MaskingType`
- Signal connection patterns match API signatures
-----END_PROSE_REVIEW_REPORT-----