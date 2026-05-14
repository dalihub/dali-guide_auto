---
title: Image View
sidebar_label: Image View
category: views-components
---

# Image View

`ImageView` is a View component for displaying static images. It supports various image formats and provides controls for fitting, sampling, masking, loading policies, and N-patch borders.

## Table of Contents

- [Creating an ImageView](#creating-an-imageview)
- [Setting the Image Source](#setting-the-image-source)
- [Fitting and Scaling](#fitting-and-scaling)
- [Image Color and Tinting](#image-color-and-tinting)
- [Alpha Masking](#alpha-masking)
- [Loading Behavior](#loading-behavior)
- [Placeholder Images](#placeholder-images)
- [N-Patch Images](#n-patch-images)
- [Pixel Area for Sub-Regions](#pixel-area-for-sub-regions)
- [Monitoring Load Status](#monitoring-load-status)

---

## Creating an ImageView

Create an `ImageView` using the static `New()` method. You can create an empty view or initialize it with an image URL.

```cpp
// Create an empty ImageView
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New();

// Create an ImageView with an image URL
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New("images/photo.jpg");
```

After creation, configure the view and add it to your layout:

```cpp
imageView.SetRequestedWidth(Dali::Ui::MATCH_PARENT)
         .SetRequestedHeight(Dali::Ui::WRAP_CONTENT);

container.Add(imageView);
```

## Setting the Image Source

Use `SetResourceUrl()` to set or change the image displayed by an existing `ImageView`. The image loads asynchronously by default.

```cpp
imageView.SetResourceUrl("images/background.png");
```

Retrieve the current URL with `GetResourceUrl()`:

```cpp
Dali::String currentUrl = imageView.GetResourceUrl();
```

To reload the current image (for example, after changing size hints or masking settings), call `Reload()`:

```cpp
imageView.Reload();
```

## Fitting and Scaling

### Fitting Mode

Control how the image fits within the view bounds using `SetFittingMode()`. The default is `Image::FittingMode::FILL`, which stretches the image to fill the view.

```cpp
// Preserve aspect ratio while fitting within bounds
imageView.SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

// Stretch to fill the entire view (may distort)
imageView.SetFittingMode(Dali::Ui::Image::FittingMode::FILL);

// Scale to cover, cropping overflow
imageView.SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);

// Display at original image size, centered
imageView.SetFittingMode(Dali::Ui::Image::FittingMode::CENTER);
```

### Sampling Mode

Control the filtering applied when scaling the image using `SetSamplingMode()`:

```cpp
// Crisp, pixelated appearance (good for pixel art)
imageView.SetSamplingMode(Dali::Ui::Image::SamplingMode::NEAREST);

// Smooth appearance (good for photos)
imageView.SetSamplingMode(Dali::Ui::Image::SamplingMode::LINEAR);

// High-quality downscaling then nearest-neighbor for upscaling
imageView.SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX_THEN_NEAREST);

// High-quality downscaling then linear filtering
imageView.SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX_THEN_LINEAR);
```

### Desired Size

Provide size hints to the image loader to reduce memory usage when displaying large images at small sizes:

```cpp
// Load the image at a reduced resolution (saves memory)
imageView.SetDesiredWidth(256);
imageView.SetDesiredHeight(256);

// Use natural image size (no hint)
imageView.SetDesiredWidth(0);
imageView.SetDesiredHeight(0);
```

After changing desired size, call `Reload()` to apply the new size hint:

```cpp
imageView.SetDesiredWidth(128)
         .SetDesiredHeight(128);
imageView.Reload();
```

## Image Color and Tinting

Apply a color multiplier to the image using `SetImageColor()`. This tints the image by multiplying each pixel's RGBA values.

```cpp
// No tint (original appearance)
imageView.SetImageColor(Dali::Ui::UiColor(1.0f, 1.0f, 1.0f, 1.0f));

// Red tint
imageView.SetImageColor(Dali::Ui::UiColor(1.0f, 0.0f, 0.0f, 1.0f));

// Grayscale appearance
imageView.SetImageColor(Dali::Ui::UiColor(0.5f, 0.5f, 0.5f, 1.0f));

// 50% transparent
imageView.SetImageColor(Dali::Ui::UiColor(1.0f, 1.0f, 1.0f, 0.5f));
```

Retrieve the current color with `GetImageColor()`:

```cpp
Dali::Ui::UiColor color = imageView.GetImageColor();
```

## Alpha Masking

Apply an alpha mask to shape the visible region of the image.

### Setting a Mask

Use `SetAlphaMaskUrl()` to specify a mask image. The alpha channel of the mask controls visibility:

```cpp
imageView.SetAlphaMaskUrl("images/circle_mask.png");
```

### Crop to Mask

By default, the full image is displayed with the mask's alpha applied. Enable `SetCropToMask()` to crop the image to the mask's bounding box:

```cpp
// Full image with mask alpha applied
imageView.SetCropToMask(false);

// Image cropped to mask bounds
imageView.SetCropToMask(true);
```

### Masking Mode

Control when masking is applied using `SetMaskingMode()`:

```cpp
// Apply mask during rendering (default)
imageView.SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING);

// Apply mask during loading (more efficient for static masks)
imageView.SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_LOADING);
```

After changing the masking mode, call `Reload()` to reapply the mask:

```cpp
imageView.SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_LOADING);
imageView.Reload();
```

## Loading Behavior

### Load Policy

Control when the image begins loading using `SetLoadPolicy()`:

```cpp
// Load immediately when the ImageView is created
imageView.SetLoadPolicy(Dali::Ui::Image::LoadPolicy::IMMEDIATE);

// Defer loading until the view is attached to the scene (default)
imageView.SetLoadPolicy(Dali::Ui::Image::LoadPolicy::ATTACHED);
```

### Release Policy

Control when the image texture is released from the cache using `SetReleasePolicy()`:

```cpp
// Release when detached from scene (default)
imageView.SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DETACHED);

// Release when the view is destroyed
imageView.SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DESTROYED);

// Never release from cache
imageView.SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::NEVER);
```

### Synchronous Loading

For small images where blocking is acceptable, enable synchronous loading:

```cpp
imageView.SetSynchronousLoading(true);
```

### Fast Track Upload

Enable background GPU upload to reduce main-thread stalls:

```cpp
imageView.SetFastTrackUpload(true);
```

### Orientation Correction

Automatically apply EXIF orientation metadata:

```cpp
imageView.SetOrientationCorrection(true);
```

## Placeholder Images

Display a placeholder image while the main image loads:

```cpp
// Set the placeholder first
imageView.SetPlaceholderUrl("images/placeholder.png");

// Then set the main image URL
imageView.SetResourceUrl("images/large_photo.jpg");
```

The placeholder displays immediately and is automatically replaced when the main image finishes loading.

## N-Patch Images

N-patch images (9-patch) allow stretchable borders with fixed corners and edges. Set the border insets to activate N-patch rendering:

```cpp
// Border: (left, top, right, bottom) in pixels
imageView.SetNPatchBorder(Dali::Vector4(10.0f, 10.0f, 10.0f, 10.0f));
```

To render only the border regions (hollow center):

```cpp
imageView.SetNPatchBorderOnly(true);
```

## Pixel Area for Sub-Regions

Display a portion of the image using `SetPixelArea()`. The area is specified in normalized coordinates (0.0 to 1.0):

```cpp
// Display the full image
imageView.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 1.0f, 1.0f));

// Display the top-left quadrant
imageView.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.5f, 0.5f));

// Display the center region
imageView.SetPixelArea(Dali::Vector4(0.25f, 0.25f, 0.5f, 0.5f));
```

The `Vector4` components are: `(x, y, width, height)`.

Retrieve the current pixel area:

```cpp
Dali::Vector4 area = imageView.GetPixelArea();
```

## Monitoring Load Status

### Resource Ready Signal

Connect to `ResourceReadySignal()` to be notified when the image finishes loading:

```cpp
void OnImageReady(Dali::Ui::View view)
{
    Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(view);
    if (imageView)
    {
        auto status = imageView.GetLoadingStatus();
        // Handle loaded image
    }
}

// Connect the signal
imageView.ResourceReadySignal().Connect(&YourClass::OnImageReady);
```

### Loading Status

Check the current loading status with `GetLoadingStatus()`:

```cpp
Dali::Ui::Visual::ResourceStatus status = imageView.GetLoadingStatus();
```

The status indicates whether the resource is loading, ready, or failed.

### DownCasting

When you receive a `View` in a signal callback, use `DownCast()` to obtain an `ImageView`:

```cpp
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(view);
if (imageView)
{
    // Successfully downcast, use imageView methods
}
