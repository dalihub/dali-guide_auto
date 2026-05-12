---
title: Image
sidebar_label: Image
category: uncategorized
---

# Image

ImageView and AnimatedImageView provide image display capabilities in dali-ui applications. They support various image formats, fitting modes, loading policies, and advanced features like pixel area selection and alpha masking.

## Table of Contents

- [Creating an ImageView](#creating-an-imageview)
- [Image Fitting and Sampling](#image-fitting-and-sampling)
- [Loading Policies](#loading-policies)
- [Pixel Area Selection](#pixel-area-selection)
- [Animated Images](#animated-images)
- [Resource Ready Signal](#resource-ready-signal)

## Creating an ImageView

Create an `ImageView` by calling `ImageView::New()` with an optional URL. The image loads asynchronously by default.

```cpp
using namespace Dali;
using namespace Dali::Ui;

// Create with a URL
ImageView imageView = ImageView::New("image.png")
  .SetRequestedWidth(200.0f)
  .SetRequestedHeight(200.0f);

// Create empty and set URL later
ImageView imageView = ImageView::New();
imageView.SetResourceUrl("image.png");
```

Use `SetImageColor()` to apply a color multiplier to the image:

```cpp
imageView.SetImageColor(UiColor(0xFF0000));  // Red tint
```

## Image Fitting and Sampling

Control how the image fits within the view bounds using `SetFittingMode()`. The `Ui::Image::FittingMode` enum provides several options:

| Value | Description |
|-------|-------------|
| `FILL` | Stretch to fill bounds (default) |
| `FIT_KEEP_ASPECT_RATIO` | Scale to fit, preserve aspect ratio |
| `OVER_FIT_KEEP_ASPECT_RATIO` | Scale to cover, crop overflow |
| `CENTER` | Keep original size, center in bounds |

```cpp
imageView.SetFittingMode(Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
```

Control the filtering algorithm used during scaling with `SetSamplingMode()`:

```cpp
imageView.SetSamplingMode(Ui::Image::SamplingMode::LINEAR);
```

Available sampling modes include `NEAREST`, `LINEAR`, `BOX`, and `LANCZOS`.

## Loading Policies

Control when images load and release from memory using load and release policies.

### Load Policy

`Ui::Image::LoadPolicy` determines when loading begins:

| Value | Description |
|-------|-------------|
| `IMMEDIATE` | Load when the ImageView is created |
| `ATTACHED` | Load when the ImageView is attached to the scene |

```cpp
imageView.SetLoadPolicy(Ui::Image::LoadPolicy::ATTACHED);
```

### Release Policy

`Ui::Image::ReleasePolicy` controls when the texture is released from cache:

| Value | Description |
|-------|-------------|
| `DETACHED` | Release when visual is detached from scene |
| `DESTROYED` | Release when visual is destroyed |
| `NEVER` | Keep in cache indefinitely |

```cpp
imageView.SetReleasePolicy(Ui::Image::ReleasePolicy::DETACHED);
```

### Synchronous Loading

For cases where blocking load is required, enable synchronous loading:

```cpp
imageView.SetSynchronousLoading(true);
```

### Fast Track Uploading

Enable background GPU upload to reduce main-thread stalls:

```cpp
imageView.SetFastTrackUpload(true);
```

## Pixel Area Selection

Display a sub-region of an image using `SetPixelArea()`. The area is specified as normalized coordinates `(x, y, width, height)` where each component ranges from 0.0 to 1.0.

```cpp
// Display the top-left quadrant
imageView.SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f));

// Display the center region
imageView.SetPixelArea(Vector4(0.25f, 0.25f, 0.5f, 0.5f));

// Display full image (default)
imageView.SetPixelArea(Vector4(0.0f, 0.0f, 1.0f, 1.0f));
```

Pixel area is animatable. Create smooth pan and zoom effects by animating the `PIXEL_AREA` property:

```cpp
#include <dali/public-api/animation/animation.h>
#include <dali/public-api/animation/key-frames.h>

KeyFrames keyFrames = KeyFrames::New();
keyFrames.Add(0.0f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));  // Full
keyFrames.Add(0.5f, Property::Value(Vector4(0.25f, 0.25f, 0.5f, 0.5f))); // Center zoom
keyFrames.Add(1.0f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));  // Back to full

Animation animation = Animation::New(3.0f);
animation.SetLooping(true);
animation.AnimateBetween(
  Property(imageView, ImageView::Property::PIXEL_AREA),
  keyFrames,
  AlphaFunction::EASE_IN_OUT);
animation.Play();
```

## Animated Images

Use `AnimatedImageView` for animated image formats like GIF. It provides playback control and loop configuration.

```cpp
AnimatedImageView animatedView = AnimatedImageView::New("animation.gif")
  .SetRequestedWidth(200.0f)
  .SetRequestedHeight(200.0f)
  .SetFittingMode(Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

// Control playback
animatedView.Play();
animatedView.Pause();
animatedView.Stop();

// Configure looping (-1 for infinite)
animatedView.SetLoopCount(3);
```

### Frame Control

Access frame information and control playback speed:

```cpp
int currentFrame = animatedView.GetCurrentFrame();
int totalFrames = animatedView.GetTotalFrame();

// Jump to a specific frame
animatedView.JumpToFrame(10);

// Adjust playback speed (values > 1 speed up, < 1 slow down)
animatedView.SetFrameSpeedFactor(1.5f);
```

### Animation Finished Signal

Connect to `AnimationFinishedSignal()` to be notified when the animation completes all loops:

```cpp
animatedView.AnimationFinishedSignal().Connect(this, &MyClass::OnAnimationFinished);
```

## Resource Ready Signal

Both `ImageView` and `AnimatedImageView` emit `ResourceReadySignal()` when the image finishes loading. Use this to defer actions until the image is ready.

```cpp
class MyController : public ConnectionTracker
{
  void CreateImageView()
  {
    mImageView = ImageView::New("image.png");
    mImageView.ResourceReadySignal().Connect(this, &MyController::OnImageReady);
  }

  void OnImageReady(View view)
  {
    // Image is now loaded and displayed
    auto status = ImageView::DownCast(view).GetLoadingStatus();
    // Proceed with dependent operations
  }

private:
  ImageView mImageView;
};
```

Check `IsResourceReady()` before connecting to avoid missing the signal on cached images:

```cpp
if (imageView.IsResourceReady())
{
  // Already loaded
}
else
{
  imageView.ResourceReadySignal().Connect(this, &MyClass::OnImageReady);
}
```

### Reloading Images

Force a reload of the current image from its source URL:

```cpp
imageView.Reload();
```
</task_progress>
</write_to_file>