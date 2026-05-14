---
title: Image
sidebar_label: Image
category: images-visuals
---

# Image

`ImageView` displays image resources with controls for fitting, scaling, masking, and loading behavior. For animated images with playback control, use `AnimatedImageView`.

## Table of Contents

- [Creating an ImageView](#creating-an-imageview)
- [Displaying Animated Images](#displaying-animated-images)
- [Fitting and Scaling](#fitting-and-scaling)
- [Image Region Display](#image-region-display)
- [Color and Tinting](#color-and-tinting)
- [Loading Behavior](#loading-behavior)
- [Alpha Masking](#alpha-masking)

## Creating an ImageView

Create an `ImageView` with a URL string pointing to your image resource. The image loads asynchronously by default.

```cpp
#include <dali-ui-foundation/public-api/image-view.h>

using namespace Dali::Ui;

// Create from URL
ImageView image = ImageView::New("images/photo.jpg");

// Create empty and set URL later
ImageView image = ImageView::New();
image.SetResourceUrl("images/photo.jpg");
```

Add the view to your layout hierarchy:

```cpp
image.SetRequestedWidth(MATCH_PARENT)
     .SetRequestedHeight(MATCH_PARENT);

container.Add(image);
```

Use `ResourceReadySignal()` to be notified when the image finishes loading:

```cpp
image.ResourceReadySignal().Connect(this, &MyClass::OnImageReady);

// Handler signature
void OnImageReady(View view)
{
  ImageView imageView = ImageView::DownCast(view);
  auto status = imageView.GetLoadingStatus();
  // Image is ready to display
}
```

## Displaying Animated Images

For animated GIF and WebP images, use `AnimatedImageView` which provides playback control:

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

AnimatedImageView animView = AnimatedImageView::New("animations/spinner.gif");

// Configure looping (-1 for infinite)
animView.SetLoopCount(-1);

// Start playback
animView.Play();

// Control playback
animView.Pause();
animView.Stop();
```

You can also provide multiple image URLs for frame-by-frame animation:

```cpp
Dali::Vector<Dali::String> frames;
frames.PushBack("frames/frame_01.png");
frames.PushBack("frames/frame_02.png");
frames.PushBack("frames/frame_03.png");

animView.SetResourceUrls(frames);
animView.Play();
```

Query playback state and frame information:

```cpp
int currentFrame = animView.GetCurrentFrame();
int totalFrames = animView.GetTotalFrame();
auto playState = animView.GetPlayState();
```

Connect to `AnimationFinishedSignal()` to detect when animation completes:

```cpp
animView.AnimationFinishedSignal().Connect(this, &MyClass::OnAnimationFinished);
```

## Fitting and Scaling

Control how the image fits within the view bounds using `SetFittingMode()`. The default fitting mode is `FILL` (stretch to fill).

```cpp
// Preserve aspect ratio, fit within bounds
image.SetFittingMode(Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

// Stretch to fill bounds (may distort)
image.SetFittingMode(Image::FittingMode::FILL);

// Preserve aspect ratio, fill bounds (may crop)
image.SetFittingMode(Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);

// Display at original size, centered
image.SetFittingMode(Image::FittingMode::CENTER);
```

Control the filtering quality when scaling with `SetSamplingMode()`:

```cpp
// Smooth scaling (default for most cases)
image.SetSamplingMode(Image::SamplingMode::LINEAR);

// Pixelated appearance for pixel art
image.SetSamplingMode(Image::SamplingMode::NEAREST);

// High quality downscaling
image.SetSamplingMode(Image::SamplingMode::BOX_THEN_LINEAR);
```

Provide size hints to the image loader for memory efficiency:

```cpp
image.SetDesiredWidth(200);
image.SetDesiredHeight(200);
```

## Image Region Display

Display a sub-region of the image using `SetPixelArea()`. The area is specified as normalized coordinates (x, y, width, height) in the range [0, 1]:

```cpp
// Display full image (default)
image.SetPixelArea(Vector4(0.0f, 0.0f, 1.0f, 1.0f));

// Display top-left quadrant
image.SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f));

// Display center zoomed region
image.SetPixelArea(Vector4(0.25f, 0.25f, 0.5f, 0.5f));
```

`PIXEL_AREA` is animatable, enabling pan and zoom effects:

```cpp
#include <dali/public-api/animation/animation.h>
#include <dali/public-api/animation/key-frames.h>

KeyFrames keyFrames = KeyFrames::New();
keyFrames.Add(0.0f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));
keyFrames.Add(0.5f, Property::Value(Vector4(0.25f, 0.25f, 0.5f, 0.5f)));
keyFrames.Add(1.0f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));

Animation animation = Animation::New(3.0f);
animation.SetLooping(true);
animation.AnimateBetween(
    Property(image, ImageView::Property::PIXEL_AREA),
    keyFrames,
    AlphaFunction::EASE_IN_OUT);
animation.Play();
```

## Color and Tinting

Apply a color multiplier to tint the image using `SetImageColor()`:

```cpp
// No tint (original appearance)
image.SetImageColor(UiColor(1.0f, 1.0f, 1.0f, 1.0f));

// Red tint
image.SetImageColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f));

// Grayscale effect
image.SetImageColor(UiColor(0.5f, 0.5f, 0.5f, 1.0f));

// 50% transparent
image.SetImageColor(UiColor(1.0f, 1.0f, 1.0f, 0.5f));
```

`AnimatedImageView` also supports image color:

```cpp
animView.SetImageColor(UiColor(1.0f, 0.5f, 0.0f, 1.0f));
```

## Loading Behavior

Control when images load and when they are released from cache.

### Load Policy

Choose between immediate loading or deferred loading until scene attachment:

```cpp
// Load when attached to scene (default, more efficient)
image.SetLoadPolicy(Image::LoadPolicy::ATTACHED);

// Load immediately when view is created
image.SetLoadPolicy(Image::LoadPolicy::IMMEDIATE);
```

### Release Policy

Control when the texture is released from the cache:

```cpp
// Release when detached from scene (default)
image.SetReleasePolicy(Image::ReleasePolicy::DETACHED);

// Release when view is destroyed
image.SetReleasePolicy(Image::ReleasePolicy::DESTROYED);

// Never release (keep cached)
image.SetReleasePolicy(Image::ReleasePolicy::NEVER);
```

### Synchronous Loading

For small images where blocking is acceptable:

```cpp
image.SetSynchronousLoading(true);
```

### Fast Track Uploading

Upload textures on a background thread to reduce main-thread stalls:

```cpp
image.SetFastTrackUpload(true);
```

### Reloading

Force a reload of the current image:

```cpp
image.Reload();
```

## Alpha Masking

Apply an alpha mask to shape the visible region of the image:

```cpp
image.SetAlphaMaskUrl("masks/circle_mask.png");
image.SetCropToMask(true);
image.SetMaskingMode(Image::MaskingType::MASKING_ON_RENDERING);
```

The mask's alpha channel determines visibility. Use `SetCropToMask()` to crop the image bounds to the mask extent.
