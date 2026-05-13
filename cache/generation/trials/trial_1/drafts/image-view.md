---
title: Image View
sidebar_label: Image View
category: views-components
---

# Image View

ImageView displays images from URLs or resource paths. It supports various image formats including JPEG, PNG, GIF (static), WebP, and BMP.

## Table of Contents

- [Creating an ImageView](#creating-an-imageview)
- [Setting the Image Source](#setting-the-image-source)
- [Fitting Modes](#fitting-modes)
- [Pixel Area and Cropping](#pixel-area-and-cropping)
- [Image Color and Tinting](#image-color-and-tinting)
- [Alpha Masking](#alpha-masking)
- [Loading Policies](#loading-policies)
- [Placeholder Images](#placeholder-images)
- [Sampling Mode and Desired Size](#sampling-mode-and-desired-size)
- [Resource Ready Signal](#resource-ready-signal)
- [N-Patch Images](#n-patch-images)

## Creating an ImageView

Create an `ImageView` using the `New()` factory method. You can create an empty view or provide an image URL directly.

```cpp
// Create an empty ImageView
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New();

// Create an ImageView with an image URL
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New("images/photo.jpg");
```

After creation, configure the view size and add it to your layout:

```cpp
imageView.SetRequestedWidth(MATCH_PARENT)
         .SetRequestedHeight(WRAP_CONTENT);
container.Add(imageView);
```

## Setting the Image Source

Use `SetResourceUrl()` to set or change the image source after creation. The URL can be a file path, resource identifier, or data URI.

```cpp
imageView.SetResourceUrl("images/gallery-large-1.jpg");
```

To retrieve the current resource URL:

```cpp
Dali::String url = imageView.GetResourceUrl();
```

## Fitting Modes

Fitting mode controls how the image scales within the view bounds. Use `SetFittingMode()` with values from `Dali::Ui::Image::FittingMode`.

```cpp
// Keep aspect ratio, fit within bounds
imageView.SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

// Stretch to fill the view, does not preserve aspect ratio
imageView.SetFittingMode(Dali::Ui::Image::FittingMode::FILL);

// Keep aspect ratio, cover the view (excess is cropped)
imageView.SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);

// Center the image at original size
imageView.SetFittingMode(Dali::Ui::Image::FittingMode::CENTER);
```

Retrieve the current fitting mode:

```cpp
Dali::Ui::Image::FittingMode mode = imageView.GetFittingMode();
```

## Pixel Area and Cropping

`SetPixelArea()` displays a sub-region of the source image. The area is specified as normalized coordinates `(x, y, width, height)` in the range `[0.0, 1.0]`.

```cpp
// Display the full image
imageView.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 1.0f, 1.0f));

// Display the top-left quadrant
imageView.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.5f, 0.5f));

// Display the center region
imageView.SetPixelArea(Dali::Vector4(0.25f, 0.25f, 0.5f, 0.5f));
```

Retrieve the current pixel area:

```cpp
Dali::Vector4 area = imageView.GetPixelArea();
```

Pixel area is animatable. You can create pan and zoom effects by animating the `PIXEL_AREA` property:

```cpp
Dali::Animation animation = Dali::Animation::New(2.0f);
animation.AnimateTo(
    Dali::Property(imageView, Dali::Ui::ImageView::Property::PIXEL_AREA),
    Dali::Vector4(0.25f, 0.25f, 0.5f, 0.5f));
animation.Play();
```

## Image Color and Tinting

`SetImageColor()` applies a color multiplier to the image. White (1.0, 1.0, 1.0, 1.0) displays the original image; other values tint or adjust transparency.

```cpp
// Original colors (no tint)
imageView.SetImageColor(Dali::UiColor(1.0f, 1.0f, 1.0f, 1.0f));

// Red tint
imageView.SetImageColor(Dali::UiColor(1.0f, 0.0f, 0.0f, 1.0f));

// Grayscale effect
imageView.SetImageColor(Dali::UiColor(0.5f, 0.5f, 0.5f, 1.0f));

// 50% transparent
imageView.SetImageColor(Dali::UiColor(1.0f, 1.0f, 1.0f, 0.5f));
```

Retrieve the current image color:

```cpp
Dali::UiColor color = imageView.GetImageColor();
```

## Alpha Masking

Apply an alpha mask to shape the visible region of the image. Use `SetAlphaMaskUrl()` to specify the mask image and `SetCropToMask()` to control whether the view crops to the mask bounds.

```cpp
// Set the alpha mask image
imageView.SetAlphaMaskUrl("images/mask.png");

// Crop the view to the mask's bounding box
imageView.SetCropToMask(true);
```

Control when masking is applied using `SetMaskingMode()`:

```cpp
// Apply mask during loading (cached with the image)
imageView.SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_LOADING);

// Apply mask during rendering (can be changed without reloading)
imageView.SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING);
```

After changing the mask or masking mode, call `Reload()` to apply changes:

```cpp
imageView.SetAlphaMaskUrl("images/new_mask.png");
imageView.Reload();
```

Retrieve mask settings:

```cpp
Dali::String maskUrl = imageView.GetAlphaMaskUrl();
bool cropToMask = imageView.IsCropToMask();
Dali::Ui::Image::MaskingType mode = imageView.GetMaskingMode();
```

## Loading Policies

Control when images load and unload using load and release policies.

### Load Policy

Set when the image begins loading:

```cpp
// Load when the view is added to the scene
imageView.SetLoadPolicy(Dali::Ui::Image::LoadPolicy::ATTACHED);

// Load immediately, before scene attachment
imageView.SetLoadPolicy(Dali::Ui::Image::LoadPolicy::IMMEDIATE);
```

### Release Policy

Set when the image data is released from memory:

```cpp
// Release when the view is removed from the scene
imageView.SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DETACHED);

// Release when the view is destroyed
imageView.SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DESTROYED);

// Never release (keep in memory)
imageView.SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::NEVER);
```

### Synchronous Loading

For small images where blocking load is acceptable:

```cpp
imageView.SetSynchronousLoading(true);
bool isSync = imageView.IsSynchronousLoading();
```

### Fast Track Upload

Enable fast GPU upload for images that need to display quickly:

```cpp
imageView.SetFastTrackUpload(true);
bool fastTrack = imageView.IsFastTrackUploadEnabled();
```

Retrieve current policies:

```cpp
Dali::Ui::Image::LoadPolicy loadPolicy = imageView.GetLoadPolicy();
Dali::Ui::Image::ReleasePolicy releasePolicy = imageView.GetReleasePolicy();
```

## Placeholder Images

Display a placeholder while the main image loads. Set the placeholder URL before setting the main image URL.

```cpp
// Create an empty ImageView
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New();

// Set the placeholder first
imageView.SetPlaceholderUrl("images/placeholder.png");

// Later, set the main image URL
imageView.SetResourceUrl("images/large_photo.jpg");
```

The placeholder displays immediately and is replaced automatically when the main image finishes loading. Retrieve the placeholder URL:

```cpp
Dali::String placeholderUrl = imageView.GetPlaceholderUrl();
```

## Sampling Mode and Desired Size

### Sampling Mode

Control texture filtering for scaled images:

```cpp
// Nearest-neighbor filtering (pixelated appearance)
imageView.SetSamplingMode(Dali::Ui::Image::SamplingMode::NEAREST);

// Box filter then nearest (good for downscaling)
imageView.SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX_THEN_NEAREST);

// Box filter then linear (smooth downscaling)
imageView.SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX_THEN_LINEAR);

// Linear filtering (smooth)
imageView.SetSamplingMode(Dali::Ui::Image::SamplingMode::LINEAR);
```

### Desired Size

Provide size hints to the loader for memory efficiency. The image loads at the specified resolution rather than full size:

```cpp
// Load at full resolution
imageView.SetDesiredWidth(0);
imageView.SetDesiredHeight(0);

// Load at reduced resolution (e.g., for thumbnails)
imageView.SetDesiredWidth(128);
imageView.SetDesiredHeight(128);
```

After changing desired size, call `Reload()` to apply the change:

```cpp
imageView.SetDesiredWidth(64);
imageView.SetDesiredHeight(64);
imageView.Reload();
```

Retrieve current settings:

```cpp
int width = imageView.GetDesiredWidth();
int height = imageView.GetDesiredHeight();
Dali::Ui::Image::SamplingMode mode = imageView.GetSamplingMode();
```

## Resource Ready Signal

Connect to `ResourceReadySignal()` to receive notification when the image finishes loading:

```cpp
void OnResourceReady(Dali::Ui::View view)
{
    Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(view);
    if (imageView)
    {
        Dali::Ui::Visual::ResourceStatus status = imageView.GetLoadingStatus();
        // status indicates success or failure
    }
}

// Connect the signal
imageView.ResourceReadySignal().Connect(this, &OnResourceReady);
```

Check loading status at any time:

```cpp
Dali::Ui::Visual::ResourceStatus status = imageView.GetLoadingStatus();
```

## N-Patch Images

N-patch images (9-patch) stretch specific regions while preserving borders and corners. Set the border definition using `SetNPatchBorder()`:

```cpp
// Border defined as (left, top, right, bottom) in pixels
imageView.SetNPatchBorder(Dali::Vector4(10.0f, 10.0f, 10.0f, 10.0f));
```

To display only the border (not the stretched center):

```cpp
imageView.SetNPatchBorderOnly(true);
```

Retrieve N-patch settings:

```cpp
Dali::Vector4 border = imageView.GetNPatchBorder();
bool borderOnly = imageView.IsNPatchBorderOnly();
```

## Additional Properties

### Orientation Correction

Enable or disable automatic orientation correction from EXIF data:

```cpp
imageView.SetOrientationCorrection(true);
bool enabled = imageView.IsOrientationCorrectionEnabled();
```

### Pre-multiplied Alpha

Control whether the image uses pre-multiplied alpha:

```cpp
imageView.SetPreMultipliedAlpha(true);
bool preMult = imageView.IsPreMultipliedAlpha();
```

### Load with View Size

Enable loading the image at the view's displayed size:

```cpp
imageView.SetImageLoadWithViewSize(true);
bool enabled = imageView.IsImageLoadWithViewSizeEnabled();
```

### Depth Index

Set the depth index for layering:

```cpp
imageView.SetDepthIndex(1);
```

## Property Access

All ImageView properties can also be accessed through the property system:

```cpp
imageView.SetProperty(Dali::Ui::ImageView::Property::FITTING_MODE,
                      static_cast<int>(Dali::Ui::Image::FittingMode::FILL));

imageView.SetProperty(Dali::Ui::ImageView::Property::IMAGE, "images/photo.jpg");

imageView.SetProperty(Dali::Ui::ImageView::Property::IMAGE_COLOR,
                      Dali::UiColor(1.0f, 0.5f, 0.0f, 1.0f));
```

## Reloading Images

Call `Reload()` to reload the current image with updated settings:

```cpp
imageView.SetDesiredWidth(256);
imageView.SetDesiredHeight(256);
imageView.Reload();
```
