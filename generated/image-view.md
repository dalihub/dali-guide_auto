---
title: Image View
sidebar_label: Image View
category: views-components
---

# Image View

`ImageView` is a `View` subclass for displaying static image resources with support for fitting modes, sampling, masking, loading policies, and N-patch borders.

## Table of Contents

- [Creating an ImageView](#creating-an-imageview)
- [Setting the Image Resource](#setting-the-image-resource)
- [Fitting and Scaling](#fitting-and-scaling)
- [Sampling Mode](#sampling-mode)
- [Desired Size](#desired-size)
- [Image Color Tinting](#image-color-tinting)
- [Pixel Area (Sub-Region Display)](#pixel-area-sub-region-display)
- [Placeholder Images](#placeholder-images)
- [Alpha Masking](#alpha-masking)
- [Loading Policies](#loading-policies)
- [N-Patch Images](#n-patch-images)
- [Monitoring Load Status](#monitoring-load-status)

## Creating an ImageView

Create an `ImageView` using the static `New()` method. You can create an empty view or initialize it with an image URL.

```cpp
// Create an empty ImageView
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New();

// Create an ImageView with an image URL
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New("images/photo.jpg");
```

The `DownCast()` method converts a base `View` handle to an `ImageView`:

```cpp
Dali::Ui::View view = /* ... */;
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(view);
if (imageView)
{
  imageView.SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
}
```

## Setting the Image Resource

Use `SetResourceUrl()` to set or change the image after creation. The image loads asynchronously by default.

```cpp
imageView.SetResourceUrl("images/background.png");
```

To retrieve the current URL:

```cpp
Dali::String url = imageView.GetResourceUrl();
```

Use `Reload()` to reload the current image from its source:

```cpp
imageView.Reload();
```

## Fitting and Scaling

Control how the image fits within the view bounds using `SetFittingMode()`. The available modes are defined in `Dali::Ui::Image::FittingMode`:

| Mode | Description |
|------|-------------|
| `FILL` | Stretch to fill the view (default, may distort) |
| `FIT_KEEP_ASPECT_RATIO` | Scale to fit while preserving aspect ratio |
| `OVER_FIT_KEEP_ASPECT_RATIO` | Scale to cover while preserving aspect ratio, cropping overflow |
| `CENTER` | Display at original size, centered |

```cpp
// Preserve aspect ratio within the view bounds
imageView.SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

// Fill the entire view, potentially cropping edges
imageView.SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);
```

Retrieve the current fitting mode:

```cpp
Dali::Ui::Image::FittingMode mode = imageView.GetFittingMode();
```

## Sampling Mode

Control the filtering applied when scaling the image using `SetSamplingMode()`. The modes are defined in `Dali::Ui::Image::SamplingMode`:

| Mode | Description |
|------|-------------|
| `NEAREST` | Nearest-neighbor sampling (pixelated appearance) |
| `LINEAR` | Bilinear filtering (smooth) |
| `BOX` | Box filter |
| `BOX_THEN_NEAREST` | Box filter then nearest |
| `BOX_THEN_LINEAR` | Box filter then linear |
| `LANCZOS` | Lanczos filter (high quality) |

```cpp
// Pixelated appearance for retro-style graphics
imageView.SetSamplingMode(Dali::Ui::Image::SamplingMode::NEAREST);

// Smooth appearance for photographs
imageView.SetSamplingMode(Dali::Ui::Image::SamplingMode::LINEAR);
```

## Desired Size

Provide size hints to the image loader using `SetDesiredWidth()` and `SetDesiredHeight()`. This allows the loader to decode at a lower resolution, saving memory and decode time.

```cpp
// Load at a reduced resolution
imageView.SetDesiredWidth(128);
imageView.SetDesiredHeight(128);
imageView.Reload();  // Required to apply new desired size
```

Retrieve the desired dimensions:

```cpp
int width = imageView.GetDesiredWidth();
int height = imageView.GetDesiredHeight();
```

For automatic sizing based on the view's layout size, use `SetImageLoadWithViewSize()`:

```cpp
imageView.SetImageLoadWithViewSize(true);
```

> **Note:** Do not use `SetImageLoadWithViewSize()` together with aspect-ratio layout on the same `ImageView`.

## Image Color Tinting

Apply a color multiplier to the image using `SetImageColor()`. White (1.0, 1.0, 1.0, 1.0) displays the original image.

```cpp
// Tint the image red
imageView.SetImageColor(Dali::Ui::UiColor(1.0f, 0.0f, 0.0f, 1.0f));

// Dim the image to 50% brightness
imageView.SetImageColor(Dali::Ui::UiColor(0.5f, 0.5f, 0.5f, 1.0f));

// Make the image semi-transparent
imageView.SetImageColor(Dali::Ui::UiColor(1.0f, 1.0f, 1.0f, 0.5f));
```

Retrieve the current color:

```cpp
Dali::Ui::UiColor color = imageView.GetImageColor();
```

## Pixel Area (Sub-Region Display)

Display a sub-region of the image using `SetPixelArea()`. The area is specified as normalized coordinates (x, y, width, height) in the range [0, 1].

```cpp
// Display the top-left quadrant
imageView.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.5f, 0.5f));

// Display the center region
imageView.SetPixelArea(Dali::Vector4(0.25f, 0.25f, 0.5f, 0.5f));

// Display the full image (default)
imageView.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 1.0f, 1.0f));
```

Retrieve the current pixel area:

```cpp
Dali::Vector4 area = imageView.GetPixelArea();
```

`PIXEL_AREA` is animatable, enabling pan and zoom effects:

```cpp
Dali::Animation animation = Dali::Animation::New(2.0f);
animation.AnimateTo(
  Dali::Property(imageView, Dali::Ui::ImageView::Property::PIXEL_AREA),
  Dali::Vector4(0.25f, 0.25f, 0.5f, 0.5f));
animation.Play();
```

## Placeholder Images

Set a placeholder image to display while the main image loads:

```cpp
imageView.SetPlaceholderUrl("images/placeholder.png");
imageView.SetResourceUrl("images/large-photo.jpg");
```

The placeholder displays immediately and is replaced automatically when the main image finishes loading.

```cpp
Dali::String placeholderUrl = imageView.GetPlaceholderUrl();
```

## Alpha Masking

Apply an alpha mask to shape the visible region of the image:

```cpp
imageView.SetAlphaMaskUrl("images/mask.png");
imageView.SetCropToMask(true);  // Crop to mask bounding box
imageView.SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING);
```

The masking modes are:

| Mode | Description |
|------|-------------|
| `MASKING_ON_RENDERING` | Apply mask during rendering (default) |
| `MASKING_ON_LOADING` | Apply mask during image loading |

```cpp
Dali::String maskUrl = imageView.GetAlphaMaskUrl();
bool cropEnabled = imageView.IsCropToMask();
Dali::Ui::Image::MaskingType mode = imageView.GetMaskingMode();
```

## Loading Policies

Control when the image loads and when it releases from the cache.

### Load Policy

| Policy | Description |
|--------|-------------|
| `IMMEDIATE` | Load when the `ImageView` is created |
| `ATTACHED` | Load when the `ImageView` is attached to the scene |

```cpp
imageView.SetLoadPolicy(Dali::Ui::Image::LoadPolicy::ATTACHED);
Dali::Ui::Image::LoadPolicy policy = imageView.GetLoadPolicy();
```

### Release Policy

| Policy | Description |
|--------|-------------|
| `DETACHED` | Release when the visual is detached from the scene |
| `DESTROYED` | Release when the visual is destroyed |
| `NEVER` | Never release from cache |

```cpp
imageView.SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DESTROYED);
Dali::Ui::Image::ReleasePolicy policy = imageView.GetReleasePolicy();
```

### Synchronous Loading

For small images where blocking load is acceptable:

```cpp
imageView.SetSynchronousLoading(true);
bool isSync = imageView.IsSynchronousLoading();
```

### Fast-Track Upload

Enable background GPU upload to reduce main-thread stalls:

```cpp
imageView.SetFastTrackUpload(true);
bool isEnabled = imageView.IsFastTrackUploadEnabled();
```

### Orientation Correction

Control whether EXIF orientation metadata is applied:

```cpp
imageView.SetOrientationCorrection(true);
bool enabled = imageView.IsOrientationCorrectionEnabled();
```

## N-Patch Images

N-patch images stretch specific regions while keeping borders intact. Set the border insets to activate N-patch rendering:

```cpp
// Border: (left, top, right, bottom) in pixels
imageView.SetNPatchBorder(Dali::Vector4(10.0f, 10.0f, 10.0f, 10.0f));
```

Render only the border regions (for frames):

```cpp
imageView.SetNPatchBorderOnly(true);
```

Retrieve border settings:

```cpp
Dali::Vector4 border = imageView.GetNPatchBorder();
bool borderOnly = imageView.IsNPatchBorderOnly();
```

## Monitoring Load Status

Connect to `ResourceReadySignal()` to be notified when the image finishes loading:

```cpp
void OnResourceReady(Dali::Ui::View view)
{
  Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(view);
  if (imageView)
  {
    Dali::Ui::Visual::ResourceStatus status = imageView.GetLoadingStatus();
    // Status: LOADING, READY, FAILED, etc.
  }
}

// Connect the signal
imageView.ResourceReadySignal().Connect(&callbackTracker, &OnResourceReady);
```

Check the current loading status:

```cpp
Dali::Ui::Visual::ResourceStatus status = imageView.GetLoadingStatus();
```

## Property Index

`ImageView` properties can be accessed via `GetProperty()` and `SetProperty()`:

| Property | Type | Description |
|----------|------|-------------|
| `IMAGE` | String | Image resource URL |
| `FITTING_MODE` | Integer | `Dali::Ui::Image::FittingMode` value |
| `SAMPLING_MODE` | Integer | `Dali::Ui::Image::SamplingMode` value |
| `DESIRED_WIDTH` | Integer | Desired rasterization width |
| `DESIRED_HEIGHT` | Integer | Desired rasterization height |
| `IMAGE_COLOR` | Vector4 | Color multiplier (RGBA) |
| `PLACEHOLDER_IMAGE` | String | Placeholder image URL |
| `PIXEL_AREA` | Vector4 | Sub-region (x, y, width, height) |
| `ALPHA_MASK_URL` | String | Alpha mask image URL |
| `CROP_TO_MASK` | Boolean | Crop to mask bounds |
| `MASKING_MODE` | Integer | `Dali::Ui::Image::MaskingType` value |
| `LOAD_POLICY` | Integer | `Dali::Ui::Image::LoadPolicy` value |
| `RELEASE_POLICY` | Integer | `Dali::Ui::Image::ReleasePolicy` value |
| `SYNCHRONOUS_LOADING` | Boolean | Enable synchronous loading |
| `FAST_TRACK_UPLOADING` | Boolean | Enable fast-track GPU upload |
| `ORIENTATION_CORRECTION` | Boolean | Apply EXIF orientation |
| `PRE_MULTIPLIED_ALPHA` | Boolean | Use pre-multiplied alpha |
| `IMAGE_LOAD_WITH_VIEW_SIZE` | Boolean | Load at view size |
| `N_PATCH_BORDER` | Vector4 | N-patch border insets |
| `N_PATCH_BORDER_ONLY` | Boolean | Render border only |

```cpp
// Using property indices
imageView.SetProperty(Dali::Ui::ImageView::Property::FITTING_MODE,
                       static_cast<int>(Dali::Ui::Image::FittingMode::CENTER));