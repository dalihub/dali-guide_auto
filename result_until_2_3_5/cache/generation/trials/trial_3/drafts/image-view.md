---
title: Image View
sidebar_label: Image View
category: views-components
---

# Image View

`Dali::Ui::ImageView` is a `Dali::Ui::View` for displaying an image resource in a dali-ui application. It provides app-facing controls for fitting, sampling, masking, loading, temporary loading images, color, and N-patch rendering.

## Table of Contents

- [Create and Load an Image](#create-and-load-an-image)
- [Fit and Sample the Image](#fit-and-sample-the-image)
- [Load at a Target Size](#load-at-a-target-size)
- [Show a Temporary Image and React to Loading](#show-a-temporary-image-and-react-to-loading)
- [Tint, Crop, and Mask](#tint-crop-and-mask)
- [Use N-Patch Images](#use-n-patch-images)
- [Control Resource Lifetime](#control-resource-lifetime)
- [Property Constants](#property-constants)

## Create and Load an Image

Create an image view with `Dali::Ui::ImageView::New()`, then assign the resource with `SetResourceUrl()`. The setter returns `Dali::Ui::ImageView&`, so image setup can be written as a fluent chain.

```cpp
Dali::Ui::ImageView thumbnail = Dali::Ui::ImageView::New();

thumbnail
  .SetResourceUrl("images/profile.jpg")
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO)
  .SetSamplingMode(Dali::Ui::Image::SamplingMode::LINEAR);
```

Use `GetResourceUrl()` when application state needs to confirm which resource is currently assigned. Use `Reload()` to request loading again for the same URL; it has no effect if no image URL has been set.

```cpp
Dali::Ui::ImageView preview = Dali::Ui::ImageView::New();

preview.SetResourceUrl("images/gallery-large-1.jpg");

Dali::String currentUrl = preview.GetResourceUrl();

// Re-request the current image resource.
preview.Reload();
```

If a generic handle has already been passed through app code, use `Dali::Ui::ImageView::DownCast()` before calling image-specific APIs. The returned handle is valid only when the input handle points to an `ImageView`.

```cpp
Dali::Ui::View itemView = GetItemView();

Dali::Ui::ImageView itemImage = Dali::Ui::ImageView::DownCast(itemView);
if(itemImage)
{
  itemImage.SetResourceUrl("images/item.png");
}
```

## Fit and Sample the Image

`SetFittingMode()` controls how the image is mapped into the `Dali::Ui::ImageView` bounds. `Dali::Ui::Image::FittingMode::FILL` stretches to fill the view and is the default fitting mode. `FIT_KEEP_ASPECT_RATIO` preserves aspect ratio inside the available area, `OVER_FIT_KEEP_ASPECT_RATIO` preserves aspect ratio while covering the area and cropping overflow, and `CENTER` keeps the image at its original size.

```cpp
Dali::Ui::ImageView banner = Dali::Ui::ImageView::New();

banner
  .SetResourceUrl("images/landscape.jpg")
  .SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO)
  .SetSamplingMode(Dali::Ui::Image::SamplingMode::LINEAR);

Dali::Ui::Image::FittingMode fittingMode = banner.GetFittingMode();
Dali::Ui::Image::SamplingMode samplingMode = banner.GetSamplingMode();
```

For pixel-art or sharp-edged assets, choose a sampling mode such as `Dali::Ui::Image::SamplingMode::NEAREST`. For photos and general UI artwork, `Dali::Ui::Image::SamplingMode::LINEAR` is usually the practical choice.

```cpp
Dali::Ui::ImageView icon = Dali::Ui::ImageView::New();

icon
  .SetResourceUrl("images/status-icon.png")
  .SetFittingMode(Dali::Ui::Image::FittingMode::CENTER)
  .SetSamplingMode(Dali::Ui::Image::SamplingMode::NEAREST);
```

Use `SetPixelArea()` to display a normalized sub-region of the image. The `Vector4` is `(x, y, width, height)` in normalized coordinates, with components in the range `[0, 1]`.

```cpp
Dali::Ui::ImageView sprite = Dali::Ui::ImageView::New();

sprite
  .SetResourceUrl("images/sprite-sheet.png")
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO)
  .SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.25f, 0.25f));

Dali::Vector4 currentArea = sprite.GetPixelArea();
```

## Load at a Target Size

`SetDesiredWidth()` and `SetDesiredHeight()` provide rasterization-size hints to the image loader, in pixels. Use them when the displayed image has a known target size and the original resource may be much larger.

```cpp
Dali::Ui::ImageView avatar = Dali::Ui::ImageView::New();

avatar
  .SetResourceUrl("images/profile-large.jpg")
  .SetDesiredWidth(128)
  .SetDesiredHeight(128)
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

int desiredWidth = avatar.GetDesiredWidth();
int desiredHeight = avatar.GetDesiredHeight();
```

`SetImageLoadWithViewSize(true)` requests loading based on the resolved view size. This is useful when layout determines the final display size and the application wants to avoid decoding the full-resolution image for a smaller view. Do not use it together with aspect-ratio layout on the same `Dali::Ui::ImageView`, because the view size is then used as the natural image size.

```cpp
Dali::Ui::ImageView cardImage = Dali::Ui::ImageView::New();

cardImage
  .SetResourceUrl("images/card-photo.jpg")
  .SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO)
  .SetImageLoadWithViewSize(true);

bool loadWithViewSize = cardImage.IsImageLoadWithViewSizeEnabled();
```

`SetOrientationCorrection()` controls whether EXIF orientation metadata is applied when loading. The implementation initializes orientation correction to `true`, which is commonly useful for photos.

```cpp
Dali::Ui::ImageView photo = Dali::Ui::ImageView::New();

photo
  .SetResourceUrl("images/exif-rotated.jpg")
  .SetOrientationCorrection(true)
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

bool orientationCorrection = photo.IsOrientationCorrectionEnabled();
```

## Show a Temporary Image and React to Loading

Use `SetPlaceholderUrl()` to show a temporary image while the main image is loading. Connect to `ResourceReadySignal()` to update application state after the image resource has finished loading and is ready to display. The signal callback receives a `Dali::Ui::View`.

```cpp
class GalleryController : public Dali::ConnectionTracker
{
public:
  void CreateImage()
  {
    mImage = Dali::Ui::ImageView::New();

    mImage
      .SetPlaceholderUrl("images/placeholder.png")
      .SetResourceUrl("images/gallery-large-3.jpg")
      .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

    mImage.ResourceReadySignal().Connect(this, &GalleryController::OnImageReady);
  }

private:
  void OnImageReady(Dali::Ui::View view)
  {
    Dali::Ui::ImageView image = Dali::Ui::ImageView::DownCast(view);
    if(image)
    {
      Dali::Ui::Visual::ResourceStatus status = image.GetLoadingStatus();
      (void)status;
    }
  }

  Dali::Ui::ImageView mImage;
};
```

`GetPlaceholderUrl()` returns the current temporary image URL, and `GetLoadingStatus()` returns the current visual resource status for the image view.

```cpp
Dali::Ui::ImageView image = Dali::Ui::ImageView::New();

image
  .SetPlaceholderUrl("images/loading.png")
  .SetResourceUrl("images/content.jpg");

Dali::String placeholderUrl = image.GetPlaceholderUrl();
Dali::Ui::Visual::ResourceStatus loadingStatus = image.GetLoadingStatus();
```

## Tint, Crop, and Mask

`SetImageColor()` applies a color multiplier to the image. It accepts a `Dali::Ui::UiColor`, which can hold direct RGBA values or a theme color token.

```cpp
Dali::Ui::ImageView favoriteIcon = Dali::Ui::ImageView::New();

favoriteIcon
  .SetResourceUrl("images/favorite.png")
  .SetImageColor(Dali::Ui::UiColor(0xFF3366))
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

Dali::Ui::UiColor imageColor = favoriteIcon.GetImageColor();
```

For alpha-masked images, assign the mask image with `SetAlphaMaskUrl()`. The mask alpha channel is applied to the main image. `SetCropToMask()` controls whether the output is cropped to the mask bounds. `SetMaskingMode()` selects whether masking is performed during rendering or loading.

```cpp
Dali::Ui::ImageView portrait = Dali::Ui::ImageView::New();

portrait
  .SetResourceUrl("images/profile.jpg")
  .SetAlphaMaskUrl("images/circle-mask.png")
  .SetCropToMask(true)
  .SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING)
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

Dali::String maskUrl = portrait.GetAlphaMaskUrl();
bool cropToMask = portrait.IsCropToMask();
Dali::Ui::Image::MaskingType maskingMode = portrait.GetMaskingMode();
```

If the source image data already uses pre-multiplied alpha, set that explicitly with `SetPreMultipliedAlpha()`.

```cpp
Dali::Ui::ImageView composited = Dali::Ui::ImageView::New();

composited
  .SetResourceUrl("images/composited.png")
  .SetPreMultipliedAlpha(true);

bool preMultipliedAlpha = composited.IsPreMultipliedAlpha();
```

## Use N-Patch Images

Use N-patch settings for scalable UI artwork whose borders must remain stable while the center stretches. `SetNPatchBorder()` takes pixel insets as `(left, top, right, bottom)`. A non-zero border activates N-patch rendering. `SetNPatchBorderOnly(true)` renders only the border regions and skips the center.

```cpp
Dali::Ui::ImageView tooltipBackground = Dali::Ui::ImageView::New();

tooltipBackground
  .SetResourceUrl("images/tooltip.9.png")
  .SetNPatchBorder(Dali::Vector4(12.0f, 12.0f, 12.0f, 12.0f))
  .SetNPatchBorderOnly(false)
  .SetFittingMode(Dali::Ui::Image::FittingMode::FILL);

Dali::Vector4 border = tooltipBackground.GetNPatchBorder();
bool borderOnly = tooltipBackground.IsNPatchBorderOnly();
```

`SetDepthIndex()` controls the rendering order of the image visual within the same layer. Higher depth values render on top of lower ones.

```cpp
Dali::Ui::ImageView shadow = Dali::Ui::ImageView::New();
Dali::Ui::ImageView foreground = Dali::Ui::ImageView::New();

shadow
  .SetResourceUrl("images/shadow.9.png")
  .SetNPatchBorder(Dali::Vector4(16.0f, 16.0f, 16.0f, 16.0f))
  .SetDepthIndex(0);

foreground
  .SetResourceUrl("images/panel.9.png")
  .SetNPatchBorder(Dali::Vector4(8.0f, 8.0f, 8.0f, 8.0f))
  .SetDepthIndex(1);
```

## Control Resource Lifetime

`SetLoadPolicy()` controls when loading starts. `Dali::Ui::Image::LoadPolicy::IMMEDIATE` loads immediately, while `Dali::Ui::Image::LoadPolicy::ATTACHED` defers loading until the view is attached to the scene.

```cpp
Dali::Ui::ImageView deferredImage = Dali::Ui::ImageView::New();

deferredImage
  .SetResourceUrl("images/offscreen-card.jpg")
  .SetLoadPolicy(Dali::Ui::Image::LoadPolicy::ATTACHED);

Dali::Ui::Image::LoadPolicy loadPolicy = deferredImage.GetLoadPolicy();
```

`SetReleasePolicy()` controls when the image texture may be released from cache. `Dali::Ui::Image::ReleasePolicy::DETACHED` releases when detached from the scene, `DESTROYED` releases when the visual is destroyed, and `NEVER` keeps it cached.

```cpp
Dali::Ui::ImageView reusableImage = Dali::Ui::ImageView::New();

reusableImage
  .SetResourceUrl("images/reusable.png")
  .SetLoadPolicy(Dali::Ui::Image::LoadPolicy::IMMEDIATE)
  .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DESTROYED);

Dali::Ui::Image::ReleasePolicy releasePolicy = reusableImage.GetReleasePolicy();
```

Use `SetSynchronousLoading(true)` only when the application needs the load to complete synchronously. Use `SetFastTrackUpload(true)` to request fast-track GPU upload; the implementation uses that path only when the image load configuration supports it.

```cpp
Dali::Ui::ImageView startupLogo = Dali::Ui::ImageView::New();

startupLogo
  .SetResourceUrl("images/startup-logo.png")
  .SetSynchronousLoading(true)
  .SetFastTrackUpload(true)
  .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::NEVER);

bool synchronous = startupLogo.IsSynchronousLoading();
bool fastTrack = startupLogo.IsFastTrackUploadEnabled();
```

## Property Constants

For application code, prefer typed setters such as `SetResourceUrl()`, `SetFittingMode()`, `SetPixelArea()`, and `SetReleasePolicy()`. The `Dali::Ui::ImageView::Property` constants expose the same image-view property indices for code that needs property metadata or integration with generic property systems.

`Dali::Ui::ImageView::Property::IMAGE` corresponds to the image resource URL. `FITTING_MODE`, `SAMPLING_MODE`, `DESIRED_WIDTH`, `DESIRED_HEIGHT`, `IMAGE_COLOR`, `PRE_MULTIPLIED_ALPHA`, `PLACEHOLDER_IMAGE`, `IMAGE_LOAD_WITH_VIEW_SIZE`, `ALPHA_MASK_URL`, `CROP_TO_MASK`, `MASKING_MODE`, `LOAD_POLICY`, `RELEASE_POLICY`, `SYNCHRONOUS_LOADING`, `FAST_TRACK_UPLOADING`, `ORIENTATION_CORRECTION`, `N_PATCH_BORDER`, `N_PATCH_BORDER_ONLY`, and `PIXEL_AREA` cover the feature-specific image-view state.

`Dali::Ui::ImageViewPropertyIndex` is the underlying property-index definition used by `Dali::Ui::ImageView::Property`. It includes the image-view property range and individual members such as `Dali::Ui::ImageViewPropertyIndex::IMAGE`, `Dali::Ui::ImageViewPropertyIndex::FITTING_MODE`, and `Dali::Ui::ImageViewPropertyIndex::PIXEL_AREA`.

```cpp
int imageProperty = Dali::Ui::ImageView::Property::IMAGE;
int pixelAreaProperty = Dali::Ui::ImageView::Property::PIXEL_AREA;

int indexedImageProperty = Dali::Ui::ImageViewPropertyIndex::IMAGE;
int indexedPixelAreaProperty = Dali::Ui::ImageViewPropertyIndex::PIXEL_AREA;

(void)imageProperty;
(void)pixelAreaProperty;
(void)indexedImageProperty;
(void)indexedPixelAreaProperty;
```
