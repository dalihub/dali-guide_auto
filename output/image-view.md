---
title: Image View
sidebar_label: Image View
category: views-components
---

# Image View

`ImageView` is a View for displaying image resources with support for various fitting modes, masking, loading policies, and N-patch borders.

## Table of Contents

- [Creating an ImageView](#creating-an-imageview)
- [Image Fitting and Scaling](#image-fitting-and-scaling)
- [Loading Behavior and Policies](#loading-behavior-and-policies)
- [Alpha Masking](#alpha-masking)
- [Pixel Area and Sub-Regions](#pixel-area-and-sub-regions)
- [N-Patch Images](#n-patch-images)
- [Resource Ready Signal](#resource-ready-signal)

## Creating an ImageView

Create an `ImageView` with or without an initial image URL. The image loads asynchronously by default.

```cpp
// Create an empty ImageView
ImageView imageView = ImageView::New();

// Create an ImageView with an image URL
ImageView imageView = ImageView::New("images/photo.jpg");

// Set or change the image URL after creation
imageView.SetResourceUrl("images/another-photo.png");
```

Use `SetPlaceholderUrl()` to display a placeholder image while the main image loads:

```cpp
ImageView imageView = ImageView::New("images/large-photo.jpg")
  .SetPlaceholderUrl("images/placeholder.png");
```

## Image Fitting and Scaling

### Fitting Mode

Control how the image fits within the view bounds using `SetFittingMode()`. The available modes are defined in `Ui::Image::FittingMode`:

- `FIT_KEEP_ASPECT_RATIO` — Scales to fit while preserving aspect ratio
- `FILL` — Stretches to fill the view (default)
- `OVER_FIT_KEEP_ASPECT_RATIO` — Scales to cover while preserving aspect ratio; excess is cropped
- `CENTER` — Displays at original size, centered

```cpp
ImageView imageView = ImageView::New("images/photo.jpg")
  .SetRequestedWidth(200.0f)
  .SetRequestedHeight(200.0f)
  .SetFittingMode(Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
```

### Sampling Mode

Control the filtering applied when scaling with `SetSamplingMode()`. The modes are defined in `Ui::Image::SamplingMode`:

```cpp
// Crisp, pixelated look for pixel art
imageView.SetSamplingMode(Ui::Image::SamplingMode::NEAREST);

// Smooth scaling for photographs
imageView.SetSamplingMode(Ui::Image::SamplingMode::LINEAR);

// High-quality downscaling then nearest-neighbor for sharp results
imageView.SetSamplingMode(Ui::Image::SamplingMode::BOX_THEN_NEAREST);
```

### Desired Size

Provide size hints to the image loader with `SetDesiredWidth()` and `SetDesiredHeight()`. This allows the loader to decode at a lower resolution, saving memory:

```cpp
ImageView imageView = ImageView::New("images/large-photo.jpg")
  .SetDesiredWidth(128)
  .SetDesiredHeight(128);

// Call Reload() to apply new desired size
imageView.SetDesiredWidth(256);
imageView.SetDesiredHeight(256);
imageView.Reload();
```

For automatic sizing based on the view's resolved layout size, use `SetImageLoadWithViewSize()`:

```cpp
ImageView imageView = ImageView::New("images/photo.jpg")
  .SetRequestedWidth(200.0f)
  .SetRequestedHeight(200.0f)
  .SetImageLoadWithViewSize(true);
```

## Loading Behavior and Policies

### Load Policy

Control when the image loads using `SetLoadPolicy()` with values from `Ui::Image::LoadPolicy`:

- `IMMEDIATE` — Loads when the `ImageView` is created
- `ATTACHED` — Loads when the `ImageView` is attached to the scene

```cpp
// Defer loading until the view is on screen
ImageView imageView = ImageView::New("images/photo.jpg")
  .SetLoadPolicy(Ui::Image::LoadPolicy::ATTACHED);
```

### Release Policy

Control when the texture is released from cache using `SetReleasePolicy()` with values from `Ui::Image::ReleasePolicy`:

- `DETACHED` — Released when the visual detaches from the scene
- `DESTROYED` — Released when the visual is destroyed
- `NEVER` — Kept in cache indefinitely

```cpp
ImageView imageView = ImageView::New("images/photo.jpg")
  .SetReleasePolicy(Ui::Image::ReleasePolicy::DESTROYED);
```

### Synchronous Loading

By default, images load asynchronously. Enable synchronous loading to block until the image is ready:

```cpp
ImageView imageView = ImageView::New("images/photo.jpg")
  .SetSynchronousLoading(true);
```

### Fast-Track Uploading

Enable background GPU upload to reduce main-thread stalls:

```cpp
ImageView imageView = ImageView::New("images/photo.jpg")
  .SetFastTrackUpload(true);
```

## Alpha Masking

Apply an alpha mask to shape the visible region of the image:

```cpp
ImageView imageView = ImageView::New("images/photo.jpg")
  .SetAlphaMaskUrl("images/mask.png")
  .SetCropToMask(true)
  .SetMaskingMode(Ui::Image::MaskingType::MASKING_ON_RENDERING);
```

The `MaskingType` values control when masking occurs:

- `MASKING_ON_RENDERING` — Mask applied during rendering
- `MASKING_ON_LOADING` — Mask baked into the texture at load time

Use `SetCropToMask(true)` to crop the output to the mask's bounding box:

```cpp
// Full image with mask alpha applied
imageView.SetCropToMask(false);

// Image cropped to mask bounds
imageView.SetCropToMask(true);
```

After changing mask properties, call `Reload()` to apply changes when using `MASKING_ON_LOADING`:

```cpp
imageView.SetAlphaMaskUrl("images/new-mask.png");
imageView.SetMaskingMode(Ui::Image::MaskingType::MASKING_ON_LOADING);
imageView.Reload();
```

## Pixel Area and Sub-Regions

Display a sub-region of the image using `SetPixelArea()`. The area is specified as normalized coordinates `(x, y, width, height)` in the range `[0, 1]`:

```cpp
// Display the top-left quadrant
ImageView imageView = ImageView::New("images/photo.jpg")
  .SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f));

// Display the center region
imageView.SetPixelArea(Vector4(0.25f, 0.25f, 0.5f, 0.5f));

// Display the full image (default)
imageView.SetPixelArea(Vector4(0.0f, 0.0f, 1.0f, 1.0f));
```

`PIXEL_AREA` is an animatable property, enabling pan and zoom effects:

```cpp
// Animate pixel area for a pan/zoom effect
KeyFrames keyFrames = KeyFrames::New();
keyFrames.Add(0.0f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));
keyFrames.Add(0.5f, Property::Value(Vector4(0.25f, 0.25f, 0.5f, 0.5f)));
keyFrames.Add(1.0f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));

Animation animation = Animation::New(3.0f);
animation.AnimateBetween(
  Property(imageView, ImageView::Property::PIXEL_AREA),
  keyFrames,
  AlphaFunction::EASE_IN_OUT);
animation.SetLooping(true);
animation.Play();
```

## N-Patch Images

N-patch images allow stretchable borders with fixed corners and edges. Set the border insets with `SetNPatchBorder()`:

```cpp
// Border: (left, top, right, bottom) in pixels
ImageView imageView = ImageView::New("images/button-npatch.9.png")
  .SetNPatchBorder(Vector4(10.0f, 10.0f, 10.0f, 10.0f));
```

To render only the border regions (hollow center), use `SetNPatchBorderOnly()`:

```cpp
imageView.SetNPatchBorderOnly(true);
```

## Resource Ready Signal

Connect to `ResourceReadySignal()` to be notified when the image finishes loading:

```cpp
class MyController : public ConnectionTracker
{
  void CreateImageView()
  {
    mImageView = ImageView::New("images/large-photo.jpg");
    mImageView.ResourceReadySignal().Connect(this, &MyController::OnImageReady);
  }

  void OnImageReady(View view)
  {
    ImageView imageView = ImageView::DownCast(view);
    if (imageView)
    {
      Ui::Visual::ResourceStatus status = imageView.GetLoadingStatus();
      // Image is ready to display
    }
  }

  ImageView mImageView;
};
```

Check the loading status at any time with `GetLoadingStatus()`:

```cpp
Ui::Visual::ResourceStatus status = imageView.GetLoadingStatus();
```

## Image Color

Apply a color multiplier to tint the image:

```cpp
// Apply a blue tint
imageView.SetImageColor(UiColor(0x4A90E2));

// Apply a semi-transparent overlay
imageView.SetImageColor(UiColor(0xFFFFFF80));
```

## Orientation Correction

Enable automatic EXIF orientation correction for JPEG images:

```cpp
ImageView imageView = ImageView::New("images/photo.jpg")
  .SetOrientationCorrection(true);
```

## DownCast

Use `ImageView::DownCast()` to safely cast a `View` or `BaseHandle` to an `ImageView`:

```cpp
View view = GetViewFromSomewhere();
ImageView imageView = ImageView::DownCast(view);
if (imageView)
{
  imageView.SetResourceUrl("images/new-photo.jpg");
}
```
