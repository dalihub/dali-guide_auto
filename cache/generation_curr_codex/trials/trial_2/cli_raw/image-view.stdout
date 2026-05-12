---
title: Image View
sidebar_label: Image View
category: views-components
---

# Image View

`Dali::Ui::ImageView` displays a static image resource in a dali-ui `View` tree and provides image-specific controls for fitting, loading, masking, tinting, sub-regions, and N-patch rendering.

## Table of Contents

- [Create and Load an Image](#create-and-load-an-image)
- [Fit, Decode Size, and Sampling](#fit-decode-size-and-sampling)
- [Tint and Display a Sub-Region](#tint-and-display-a-sub-region)
- [Loading Completion](#loading-completion)
- [Mask an Image](#mask-an-image)
- [Control Loading and Resource Lifetime](#control-loading-and-resource-lifetime)
- [Use N-Patch Images](#use-n-patch-images)
- [ImageView Properties](#imageview-properties)

## Create and Load an Image

Use `Dali::Ui::ImageView::New` to create the view, then set the image URL with `Dali::Ui::ImageView::SetResourceUrl`. The dali-ui app-facing object is still a `Dali::Ui::View`, so application code can keep generic view references and downcast only when it needs image-specific APIs.

```cpp
#include <dali-ui-foundation/public-api/image-view.h>

Dali::Ui::ImageView CreateThumbnail()
{
  return Dali::Ui::ImageView::New()
    .SetResourceUrl("images/photo.jpg")
    .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
}
```

`Dali::Ui::ImageView::New` also has a URL overload for the common case where the resource is known at construction time.

```cpp
Dali::Ui::ImageView image =
  Dali::Ui::ImageView::New("images/profile.png")
    .SetFittingMode(Dali::Ui::Image::FittingMode::FILL);
```

When a handler receives a generic `Dali::Ui::View`, use `Dali::Ui::ImageView::DownCast` before calling image-specific methods such as `Dali::Ui::ImageView::GetResourceUrl` or `Dali::Ui::ImageView::Reload`.

```cpp
void RefreshImage(Dali::Ui::View view)
{
  Dali::Ui::ImageView image = Dali::Ui::ImageView::DownCast(view);
  if(image)
  {
    image.Reload();
  }
}
```

## Fit, Decode Size, and Sampling

`Dali::Ui::ImageView::SetFittingMode` controls how the image is placed inside the view bounds. Use `Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO` when the full image should remain visible, `Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO` when cropping is acceptable, and `Dali::Ui::Image::FittingMode::FILL` when stretching to the view bounds is desired.

```cpp
Dali::Ui::ImageView preview =
  Dali::Ui::ImageView::New("images/wide-banner.jpg")
    .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

Dali::Ui::Image::FittingMode mode = preview.GetFittingMode();
```

Use `Dali::Ui::ImageView::SetDesiredWidth` and `Dali::Ui::ImageView::SetDesiredHeight` as decode-size hints in pixels. Pair them with `Dali::Ui::ImageView::SetSamplingMode` when the image will be displayed at a smaller or fixed raster size.

```cpp
Dali::Ui::ImageView icon =
  Dali::Ui::ImageView::New("images/icon-large.png")
    .SetDesiredWidth(64)
    .SetDesiredHeight(64)
    .SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX_THEN_LINEAR)
    .SetFittingMode(Dali::Ui::Image::FittingMode::FILL);

int desiredWidth  = icon.GetDesiredWidth();
int desiredHeight = icon.GetDesiredHeight();
Dali::Ui::Image::SamplingMode sampling = icon.GetSamplingMode();
```

For responsive layouts where the view size should drive the image load size, enable `Dali::Ui::ImageView::SetImageLoadWithViewSize`. This makes the resolved view size the image load-size input.

```cpp
Dali::Ui::ImageView cardImage =
  Dali::Ui::ImageView::New("images/card-photo.jpg")
    .SetImageLoadWithViewSize(true)
    .SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);

bool usesViewSize = cardImage.IsImageLoadWithViewSizeEnabled();
```

## Tint and Display a Sub-Region

`Dali::Ui::ImageView::SetImageColor` applies a `Dali::Ui::UiColor` multiplier to the image. This is useful for disabled states, selection states, or monochrome icon tinting.

```cpp
Dali::Ui::ImageView tinted =
  Dali::Ui::ImageView::New("images/status-icon.png")
    .SetImageColor(Dali::Ui::UiColor(0x66CCFFFF));

Dali::Ui::UiColor currentTint = tinted.GetImageColor();
```

`Dali::Ui::ImageView::SetPixelArea` displays a normalized sub-region of the image as `(x, y, width, height)`. For example, `(0.0f, 0.0f, 0.5f, 0.5f)` selects the upper-left quarter of the source image.

```cpp
Dali::Ui::ImageView sprite =
  Dali::Ui::ImageView::New("images/spritesheet.png")
    .SetFittingMode(Dali::Ui::Image::FittingMode::FILL)
    .SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.5f, 0.5f));

Dali::Vector4 area = sprite.GetPixelArea();

sprite.SetPixelArea(Dali::Vector4(0.5f, 0.0f, 0.5f, 0.5f));
```

If the source image contains alpha data that is already pre-multiplied, set `Dali::Ui::ImageView::SetPreMultipliedAlpha`.

```cpp
Dali::Ui::ImageView premultiplied =
  Dali::Ui::ImageView::New("images/premultiplied.png")
    .SetPreMultipliedAlpha(true);

bool enabled = premultiplied.IsPreMultipliedAlpha();
```

## Loading Completion

`Dali::Ui::ImageView::SetPlaceholderUrl` assigns an image to show while the main image is loading. Use `Dali::Ui::ImageView::ResourceReadySignal` to run code after the image resource is ready.

```cpp
class GalleryController : public Dali::ConnectionTracker
{
public:
  Dali::Ui::ImageView CreateImage()
  {
    Dali::Ui::ImageView image =
      Dali::Ui::ImageView::New()
        .SetPlaceholderUrl("images/placeholder.png")
        .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

    image.ResourceReadySignal().Connect(this, &GalleryController::OnImageReady);
    image.SetResourceUrl("images/gallery-large.jpg");
    return image;
  }

private:
  void OnImageReady(Dali::Ui::View view)
  {
    Dali::Ui::ImageView image = Dali::Ui::ImageView::DownCast(view);
    if(image)
    {
      Dali::Ui::Visual::ResourceStatus status = image.GetLoadingStatus();
      Dali::String url = image.GetResourceUrl();
    }
  }
};
```

`Dali::Ui::ImageView::Reload` reloads the currently assigned URL. This is useful when the URL string is unchanged but the backing resource may have changed.

```cpp
void ReloadIfReady(Dali::Ui::ImageView image)
{
  if(image.GetResourceUrl() != "")
  {
    image.Reload();
  }
}
```

## Mask an Image

Use `Dali::Ui::ImageView::SetAlphaMaskUrl` to apply the alpha channel of another image as a mask. `Dali::Ui::ImageView::SetCropToMask` controls whether the result is cropped to the mask bounds, and `Dali::Ui::ImageView::SetMaskingMode` selects when masking is applied.

```cpp
Dali::Ui::ImageView avatar =
  Dali::Ui::ImageView::New("images/profile-photo.jpg")
    .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO)
    .SetAlphaMaskUrl("images/circle-mask.png")
    .SetCropToMask(true)
    .SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING);

Dali::String maskUrl = avatar.GetAlphaMaskUrl();
bool cropToMask = avatar.IsCropToMask();
Dali::Ui::Image::MaskingType maskingMode = avatar.GetMaskingMode();
```

Use `Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING` when the mask should be applied during rendering. Use `Dali::Ui::Image::MaskingType::MASKING_ON_LOADING` when the masked result should be prepared during loading.

```cpp
avatar
  .SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_LOADING)
  .Reload();
```

## Control Loading and Resource Lifetime

`Dali::Ui::ImageView::SetLoadPolicy` controls when loading starts. `Dali::Ui::Image::LoadPolicy::IMMEDIATE` loads when the `Dali::Ui::ImageView` is created, while `Dali::Ui::Image::LoadPolicy::ATTACHED` defers loading until the view is attached to the scene.

```cpp
Dali::Ui::ImageView deferred =
  Dali::Ui::ImageView::New("images/large-photo.jpg")
    .SetLoadPolicy(Dali::Ui::Image::LoadPolicy::ATTACHED);

Dali::Ui::Image::LoadPolicy loadPolicy = deferred.GetLoadPolicy();
```

`Dali::Ui::ImageView::SetReleasePolicy` controls when the image texture can be released. `Dali::Ui::Image::ReleasePolicy::DETACHED` releases when detached, `Dali::Ui::Image::ReleasePolicy::DESTROYED` releases when the visual is destroyed, and `Dali::Ui::Image::ReleasePolicy::NEVER` keeps the resource cached.

```cpp
Dali::Ui::ImageView cached =
  Dali::Ui::ImageView::New("images/reused-thumbnail.jpg")
    .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::NEVER);

Dali::Ui::Image::ReleasePolicy releasePolicy = cached.GetReleasePolicy();
```

For special loading paths, `Dali::Ui::ImageView` also exposes synchronous loading, background fast-track uploading, and orientation correction controls.

```cpp
Dali::Ui::ImageView photo =
  Dali::Ui::ImageView::New("images/exif-rotated.jpg")
    .SetSynchronousLoading(false)
    .SetFastTrackUpload(true)
    .SetOrientationCorrection(true);

bool synchronous = photo.IsSynchronousLoading();
bool fastTrack = photo.IsFastTrackUploadEnabled();
bool orientationCorrection = photo.IsOrientationCorrectionEnabled();
```

## Use N-Patch Images

N-patch images are useful for stretchable UI assets such as button backgrounds and panels. Use `Dali::Ui::ImageView::SetNPatchBorder` to provide border insets as `(left, top, right, bottom)` pixel values. A non-zero border activates N-patch rendering.

```cpp
Dali::Ui::ImageView buttonBackground =
  Dali::Ui::ImageView::New("images/button-up.9.png")
    .SetNPatchBorder(Dali::Vector4(12.0f, 12.0f, 12.0f, 12.0f));

Dali::Vector4 border = buttonBackground.GetNPatchBorder();
```

Use `Dali::Ui::ImageView::SetNPatchBorderOnly` when only the border regions should render and the center region should be omitted.

```cpp
Dali::Ui::ImageView focusRing =
  Dali::Ui::ImageView::New("images/focus-ring.9.png")
    .SetNPatchBorder(Dali::Vector4(8.0f, 8.0f, 8.0f, 8.0f))
    .SetNPatchBorderOnly(true);

bool borderOnly = focusRing.IsNPatchBorderOnly();
```

`Dali::Ui::ImageView::SetDepthIndex` controls the image visual ordering within the same layer; higher values render on top.

```cpp
Dali::Ui::ImageView overlay =
  Dali::Ui::ImageView::New("images/highlight.png")
    .SetDepthIndex(10);
```

## ImageView Properties

Prefer the typed `Dali::Ui::ImageView` setters in application code. The property constants are still part of the `ImageView` feature surface and are useful when a property index is needed by a framework path that works with generic properties.

`Dali::Ui::ImageView::Property` exposes the application-facing property constants:

| Property constant | Typed API |
| --- | --- |
| `Dali::Ui::ImageView::Property::IMAGE` | `Dali::Ui::ImageView::SetResourceUrl`, `Dali::Ui::ImageView::GetResourceUrl` |
| `Dali::Ui::ImageView::Property::FITTING_MODE` | `Dali::Ui::ImageView::SetFittingMode`, `Dali::Ui::ImageView::GetFittingMode` |
| `Dali::Ui::ImageView::Property::SAMPLING_MODE` | `Dali::Ui::ImageView::SetSamplingMode`, `Dali::Ui::ImageView::GetSamplingMode` |
| `Dali::Ui::ImageView::Property::DESIRED_WIDTH` | `Dali::Ui::ImageView::SetDesiredWidth`, `Dali::Ui::ImageView::GetDesiredWidth` |
| `Dali::Ui::ImageView::Property::DESIRED_HEIGHT` | `Dali::Ui::ImageView::SetDesiredHeight`, `Dali::Ui::ImageView::GetDesiredHeight` |
| `Dali::Ui::ImageView::Property::IMAGE_COLOR` | `Dali::Ui::ImageView::SetImageColor`, `Dali::Ui::ImageView::GetImageColor` |
| `Dali::Ui::ImageView::Property::PIXEL_AREA` | `Dali::Ui::ImageView::SetPixelArea`, `Dali::Ui::ImageView::GetPixelArea` |
| `Dali::Ui::ImageView::Property::PLACEHOLDER_IMAGE` | `Dali::Ui::ImageView::SetPlaceholderUrl`, `Dali::Ui::ImageView::GetPlaceholderUrl` |
| `Dali::Ui::ImageView::Property::ALPHA_MASK_URL` | `Dali::Ui::ImageView::SetAlphaMaskUrl`, `Dali::Ui::ImageView::GetAlphaMaskUrl` |
| `Dali::Ui::ImageView::Property::CROP_TO_MASK` | `Dali::Ui::ImageView::SetCropToMask`, `Dali::Ui::ImageView::IsCropToMask` |
| `Dali::Ui::ImageView::Property::MASKING_MODE` | `Dali::Ui::ImageView::SetMaskingMode`, `Dali::Ui::ImageView::GetMaskingMode` |
| `Dali::Ui::ImageView::Property::LOAD_POLICY` | `Dali::Ui::ImageView::SetLoadPolicy`, `Dali::Ui::ImageView::GetLoadPolicy` |
| `Dali::Ui::ImageView::Property::RELEASE_POLICY` | `Dali::Ui::ImageView::SetReleasePolicy`, `Dali::Ui::ImageView::GetReleasePolicy` |
| `Dali::Ui::ImageView::Property::SYNCHRONOUS_LOADING` | `Dali::Ui::ImageView::SetSynchronousLoading`, `Dali::Ui::ImageView::IsSynchronousLoading` |
| `Dali::Ui::ImageView::Property::FAST_TRACK_UPLOADING` | `Dali::Ui::ImageView::SetFastTrackUpload`, `Dali::Ui::ImageView::IsFastTrackUploadEnabled` |
| `Dali::Ui::ImageView::Property::IMAGE_LOAD_WITH_VIEW_SIZE` | `Dali::Ui::ImageView::SetImageLoadWithViewSize`, `Dali::Ui::ImageView::IsImageLoadWithViewSizeEnabled` |
| `Dali::Ui::ImageView::Property::ORIENTATION_CORRECTION` | `Dali::Ui::ImageView::SetOrientationCorrection`, `Dali::Ui::ImageView::IsOrientationCorrectionEnabled` |
| `Dali::Ui::ImageView::Property::PRE_MULTIPLIED_ALPHA` | `Dali::Ui::ImageView::SetPreMultipliedAlpha`, `Dali::Ui::ImageView::IsPreMultipliedAlpha` |
| `Dali::Ui::ImageView::Property::N_PATCH_BORDER` | `Dali::Ui::ImageView::SetNPatchBorder`, `Dali::Ui::ImageView::GetNPatchBorder` |
| `Dali::Ui::ImageView::Property::N_PATCH_BORDER_ONLY` | `Dali::Ui::ImageView::SetNPatchBorderOnly`, `Dali::Ui::ImageView::IsNPatchBorderOnly` |

`Dali::Ui::ImageViewPropertyIndex` owns the underlying index range for these properties. `Dali::Ui::ImageView::Property` maps to those indices for application code, including `Dali::Ui::ImageViewPropertyIndex::IMAGE`, `Dali::Ui::ImageViewPropertyIndex::FITTING_MODE`, `Dali::Ui::ImageViewPropertyIndex::SAMPLING_MODE`, `Dali::Ui::ImageViewPropertyIndex::IMAGE_COLOR`, `Dali::Ui::ImageViewPropertyIndex::PRE_MULTIPLIED_ALPHA`, `Dali::Ui::ImageViewPropertyIndex::PLACEHOLDER_IMAGE`, `Dali::Ui::ImageViewPropertyIndex::ALPHA_MASK_URL`, `Dali::Ui::ImageViewPropertyIndex::CROP_TO_MASK`, `Dali::Ui::ImageViewPropertyIndex::MASKING_MODE`, `Dali::Ui::ImageViewPropertyIndex::DESIRED_WIDTH`, `Dali::Ui::ImageViewPropertyIndex::DESIRED_HEIGHT`, `Dali::Ui::ImageViewPropertyIndex::LOAD_POLICY`, `Dali::Ui::ImageViewPropertyIndex::RELEASE_POLICY`, `Dali::Ui::ImageViewPropertyIndex::SYNCHRONOUS_LOADING`, `Dali::Ui::ImageViewPropertyIndex::IMAGE_LOAD_WITH_VIEW_SIZE`, `Dali::Ui::ImageViewPropertyIndex::FAST_TRACK_UPLOADING`, `Dali::Ui::ImageViewPropertyIndex::ORIENTATION_CORRECTION`, `Dali::Ui::ImageViewPropertyIndex::N_PATCH_BORDER`, `Dali::Ui::ImageViewPropertyIndex::N_PATCH_BORDER_ONLY`, and `Dali::Ui::ImageViewPropertyIndex::PIXEL_AREA`.

`Dali::Ui::ImageViewPropertyIndex::PropertyRange` reserves the property index range for the `ImageView` feature.
