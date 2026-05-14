---
title: Image View
sidebar_label: Image View
category: views-components
---

# Image View

`ImageView` is a view component that displays static images from URLs, file paths, or in-memory pixel data. It supports various image formats including JPEG, PNG, WebP, and GIF (static frame). For animated images, use `AnimatedImageView`.

## Table of Contents

- [Creating an ImageView](#creating-an-imageview)
- [Setting the Image Source](#setting-the-image-source)
- [Fitting Modes](#fitting-modes)
- [Sampling and Desired Size](#sampling-and-desired-size)
- [Alpha Masking](#alpha-masking)
- [Pixel Area for Partial Display](#pixel-area-for-partial-display)
- [Image Color Tinting](#image-color-tinting)
- [Loading Policies and Resource Management](#loading-policies-and-resource-management)
- [Placeholder Images](#placeholder-images)
- [N-Patch Images](#n-patch-images)
- [Resource Ready Signal](#resource-ready-signal)

## Creating an ImageView

Create an `ImageView` using the `New()` factory method. You can create an empty view or provide a URL at construction.

```cpp
// Create an empty ImageView
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New();

// Create an ImageView with a URL
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New("path/to/image.jpg");
```

Use fluent chaining to configure the view during creation:

```cpp
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New("path/to/image.png")
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT)
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
```

## Setting the Image Source

After creating an `ImageView`, you can change the image source using `SetResourceUrl()`.

```cpp
imageView.SetResourceUrl("path/to/another-image.jpg");
```

To retrieve the current resource URL:

```cpp
Dali::String url = imageView.GetResourceUrl();
```

You can also set the image using the `IMAGE` property:

```cpp
imageView.SetProperty(Dali::Ui::ImageView::Property::IMAGE, "path/to/image.jpg");
```

## Fitting Modes

`FittingMode` controls how the image is scaled to fit the view area. Use `SetFittingMode()` to specify the mode.

| Mode | Description |
|------|-------------|
| `FIT_KEEP_ASPECT_RATIO` | Scales the image to fit within the view while preserving aspect ratio. The image may not fill the entire view. |
| `FILL` | Stretches the image to fill the entire view area. Aspect ratio may be distorted. |
| `OVER_FIT_KEEP_ASPECT_RATIO` | Scales the image to cover the view while preserving aspect ratio. Some portions may be cropped. |
| `CENTER` | Centers the image at its original size without scaling. |

```cpp
imageView.SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
```

Retrieve the current fitting mode:

```cpp
Dali::Ui::Image::FittingMode mode = imageView.GetFittingMode();
```

## Sampling and Desired Size

### Sampling Mode

`SamplingMode` controls the filtering method used when scaling images. This is particularly visible when a small image is displayed larger than its original size.

| Mode | Description |
|------|-------------|
| `NEAREST` | Nearest-neighbor sampling. Fast but may appear pixelated. |
| `LINEAR` | Bilinear filtering. Smooth results for most cases. |
| `BOX_THEN_NEAREST` | Box filter followed by nearest-neighbor. Good for downscaling. |
| `BOX_THEN_LINEAR` | Box filter followed by linear filtering. Smooth downscaling. |

```cpp
imageView.SetSamplingMode(Dali::Ui::Image::SamplingMode::LINEAR);
```

### Desired Size

`DesiredWidth` and `DesiredHeight` provide hints to the image loader about the target resolution. This is useful for loading large images at reduced resolution to save memory.

```cpp
imageView.SetDesiredWidth(128);
imageView.SetDesiredHeight(128);
imageView.Reload(); // Required to apply the new desired size
```

Retrieve the current desired dimensions:

```cpp
int width = imageView.GetDesiredWidth();
int height = imageView.GetDesiredHeight();
```

## Alpha Masking

`ImageView` supports alpha masking to apply custom shapes to images. Use `SetAlphaMaskUrl()` to specify a mask image where the alpha channel defines the visible region.

```cpp
imageView.SetAlphaMaskUrl("path/to/mask.png");
```

### Crop to Mask

By default, the image retains its original size with the mask applied. Enable `CropToMask` to crop the result to the mask's bounding box.

```cpp
imageView.SetCropToMask(true);
```

### Masking Mode

Control when the masking is applied using `SetMaskingMode()`:

| Mode | Description |
|------|-------------|
| `MASKING_ON_RENDERING` | Applies the mask during rendering. Supports dynamic mask changes. |
| `MASKING_ON_LOADING` | Applies the mask during image loading. More efficient but requires `Reload()` after mask changes. |

```cpp
imageView.SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING);
```

Retrieve the current masking configuration:

```cpp
Dali::String maskUrl = imageView.GetAlphaMaskUrl();
bool cropToMask = imageView.IsCropToMask();
Dali::Ui::Image::MaskingType maskingMode = imageView.GetMaskingMode();
```

## Pixel Area for Partial Display

`PixelArea` allows displaying a sub-region of the image. The area is specified as normalized coordinates (x, y, width, height) in the range [0, 1].

```cpp
// Display the top-left quadrant
imageView.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.5f, 0.5f));

// Display the center region
imageView.SetPixelArea(Dali::Vector4(0.25f, 0.25f, 0.5f, 0.5f));
```

Retrieve the current pixel area:

```cpp
Dali::Vector4 pixelArea = imageView.GetPixelArea();
```

The `PIXEL_AREA` property is animatable, enabling smooth pan and zoom effects:

```cpp
Dali::Animation animation = Dali::Animation::New(2.0f);
animation.AnimateTo(
  Dali::Property(imageView, Dali::Ui::ImageView::Property::PIXEL_AREA),
  Dali::Vector4(0.25f, 0.25f, 0.5f, 0.5f));
animation.Play();
```

## Image Color Tinting

`SetImageColor()` applies a color multiplier to the image. Use white (1.0, 1.0, 1.0, 1.0) to display the original image, or apply tinting for visual effects.

```cpp
// No tint (original colors)
imageView.SetImageColor(Dali::UiColor(1.0f, 1.0f, 1.0f, 1.0f));

// Red tint
imageView.SetImageColor(Dali::UiColor(1.0f, 0.0f, 0.0f, 1.0f));

// 50% transparent
imageView.SetImageColor(Dali::UiColor(1.0f, 1.0f, 1.0f, 0.5f));

// Grayscale effect
imageView.SetImageColor(Dali::UiColor(0.5f, 0.5f, 0.5f, 1.0f));
```

Retrieve the current image color:

```cpp
Dali::UiColor color = imageView.GetImageColor();
```

## Loading Policies and Resource Management

### Load Policy

`LoadPolicy` controls when image loading begins.

| Policy | Description |
|--------|-------------|
| `ATTACHED` | Loading starts when the view is added to the scene. |
| `IMMEDIATE` | Loading starts immediately when the URL is set, before scene attachment. |

```cpp
imageView.SetLoadPolicy(Dali::Ui::Image::LoadPolicy::ATTACHED);
```

### Release Policy

`ReleasePolicy` controls when the image data is released from memory.

| Policy | Description |
|--------|-------------|
| `DETACHED` | Releases when the view is removed from the scene. |
| `DESTROYED` | Releases when the view is destroyed. |
| `NEVER` | Never releases; keeps the image in memory. |

```cpp
imageView.SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DETACHED);
```

### Synchronous Loading

For small images where blocking load is acceptable, enable synchronous loading:

```cpp
imageView.SetSynchronousLoading(true);
```

### Fast Track Upload

Enable fast track uploading for faster GPU upload when the image size matches the view size:

```cpp
imageView.SetFastTrackUpload(true);
```

Retrieve the current policies:

```cpp
Dali::Ui::Image::LoadPolicy loadPolicy = imageView.GetLoadPolicy();
Dali::Ui::Image::ReleasePolicy releasePolicy = imageView.GetReleasePolicy();
bool syncLoading = imageView.IsSynchronousLoading();
bool fastTrack = imageView.IsFastTrackUploadEnabled();
```

## Placeholder Images

Set a placeholder image to display while the main image is loading:

```cpp
imageView.SetPlaceholderUrl("path/to/placeholder.png");
```

The placeholder is automatically replaced when the main image finishes loading.

```cpp
Dali::String placeholderUrl = imageView.GetPlaceholderUrl();
```

## N-Patch Images

N-patch (9-patch) images allow stretchable regions with fixed borders. Set the border using `SetNPatchBorder()`:

```cpp
// Border: left, right, top, bottom
imageView.SetNPatchBorder(Dali::Vector4(10.0f, 10.0f, 10.0f, 10.0f));
```

To display only the border (for frame-like UI elements):

```cpp
imageView.SetNPatchBorderOnly(true);
```

Retrieve N-patch settings:

```cpp
Dali::Vector4 border = imageView.GetNPatchBorder();
bool borderOnly = imageView.IsNPatchBorderOnly();
```

## Resource Ready Signal

Connect to `ResourceReadySignal()` to receive notification when the image finishes loading:

```cpp
void OnResourceReady(Dali::Ui::ImageView view)
{
  Dali::Ui::Visual::ResourceStatus status = view.GetLoadingStatus();
  // Handle loaded image
}

imageView.ResourceReadySignal().Connect(&OnResourceReady);
```

Check the loading status at any time:

```cpp
Dali::Ui::Visual::ResourceStatus status = imageView.GetLoadingStatus();
```

### Reloading Images

Call `Reload()` to reload the current image. This is useful after changing properties that affect loading, such as desired size or masking mode:

```cpp
imageView.SetDesiredWidth(256);
imageView.SetDesiredHeight(256);
imageView.Reload();
```

## Additional Properties

| Property | Description |
|----------|-------------|
| `ORIENTATION_CORRECTION` | Enable/disable automatic orientation correction from EXIF data. |
| `PRE_MULTIPLIED_ALPHA` | Use pre-multiplied alpha blending. |
| `IMAGE_LOAD_WITH_VIEW_SIZE` | Use view size as a hint for image loading resolution. |
| `DEPTH_INDEX` | Set the depth index for rendering order. |

```cpp
imageView.SetOrientationCorrection(true);
imageView.SetPreMultipliedAlpha(false);
imageView.SetImageLoadWithViewSize(true);
imageView.SetDepthIndex(0);
```

Retrieve these settings:

```cpp
bool orientationCorrection = imageView.IsOrientationCorrectionEnabled();
bool preMultiplied = imageView.IsPreMultipliedAlpha();
bool loadWithViewSize = imageView.IsImageLoadWithViewSizeEnabled();
```

## Type Casting

Use `DownCast()` to safely cast a `BaseHandle` or `View` to an `ImageView`:

```cpp
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(view);
if (imageView)
{
  // Successfully cast to ImageView
  imageView.SetResourceUrl("new-image.jpg");
}
