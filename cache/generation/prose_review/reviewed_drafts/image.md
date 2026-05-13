---
title: Image
sidebar_label: Image
category: uncategorized
---

# Image

ImageView and AnimatedImageView provide high-level components for displaying static and animated images in dali-ui applications.

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

- `Image::ReleasePolicy::DETACHED` — Release when removed from scene
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

Control how the image fits within the view bounds using `SetFittingMode()`:

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

`AnimatedImageView` displays animated image formats such as GIF. It provides playback control methods:

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

### N-Patch Borders

For stretchable UI elements like buttons and frames, use N-patch borders:

```cpp
// Set border insets (left, top, right, bottom)
imageView.SetNPatchBorder(Vector4(10, 10, 10, 10));

// Render only the border (for hollow frames)
imageView.SetNPatchBorderOnly(true);
