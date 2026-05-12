---
title: Image View
sidebar_label: Image View
category: views-components
---

# Image View

`Dali::Ui::ImageView` displays a static image resource in a dali-ui application and provides image-specific controls for loading, fitting, masking, tinting, cropping, and N-patch rendering.

## Table of Contents

- [Create and Update an Image](#create-and-update-an-image)
- [Fit, Sample, and Decode at a Target Size](#fit-sample-and-decode-at-a-target-size)
- [Show Placeholders and React to Loading](#show-placeholders-and-react-to-loading)
- [Tint and Crop the Displayed Region](#tint-and-crop-the-displayed-region)
- [Apply Alpha Masks](#apply-alpha-masks)
- [Control Loading and Cache Lifetime](#control-loading-and-cache-lifetime)
- [Render Stretchable N-Patch Images](#render-stretchable-n-patch-images)
- [Property Constants](#property-constants)

## Create and Update an Image

Create an `ImageView` with `Dali::Ui::ImageView::New()`, then set the displayed image with `SetResourceUrl()`. The image URL can be changed later with another `SetResourceUrl()` call, and `Reload()` reloads the current URL.

```cpp
Dali::Ui::ImageView thumbnail = Dali::Ui::ImageView::New();

thumbnail
  .SetResourceUrl("images/gallery/photo-01.jpg")
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
```

Use the URL accessors when synchronizing application state with the view.

```cpp
void SelectImage(Dali::Ui::ImageView imageView, const Dali::String& url)
{
  if(imageView.GetResourceUrl() != url)
  {
    imageView.SetResourceUrl(url);
  }
}

void RefreshCurrentImage(Dali::Ui::ImageView imageView)
{
  if(!imageView.GetResourceUrl().empty())
  {
    imageView.Reload();
  }
}
```

When a generic handle is passed through app code, use `Dali::Ui::ImageView::DownCast()` before calling image-specific APIs.

```cpp
void ConfigureIfImage(Dali::BaseHandle handle)
{
  Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(handle);

  if(imageView)
  {
    imageView.SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
  }
}
```

## Fit, Sample, and Decode at a Target Size

`SetFittingMode()` controls how the image is fitted into the view bounds. `Dali::Ui::Image::FittingMode::FILL` stretches to fill the bounds, `FIT_KEEP_ASPECT_RATIO` preserves the aspect ratio inside the bounds, `OVER_FIT_KEEP_ASPECT_RATIO` preserves the aspect ratio while cropping the outside, and `CENTER` keeps the image at its original size.

```cpp
Dali::Ui::ImageView cover = Dali::Ui::ImageView::New();

cover
  .SetResourceUrl("images/header.jpg")
  .SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);
```

`SetSamplingMode()` chooses the filter used when scaling the image. For small thumbnails, combine a target decode size with an appropriate sampling mode.

```cpp
Dali::Ui::ImageView thumbnail = Dali::Ui::ImageView::New();

thumbnail
  .SetResourceUrl("images/large-photo.jpg")
  .SetDesiredWidth(320)
  .SetDesiredHeight(180)
  .SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX_THEN_LINEAR)
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
```

`SetImageLoadWithViewSize(true)` makes the current view size drive the image load size. Use it when the displayed size is the desired decode size.

```cpp
Dali::Ui::ImageView preview = Dali::Ui::ImageView::New();

preview
  .SetResourceUrl("images/preview.jpg")
  .SetImageLoadWithViewSize(true)
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

bool loadWithViewSize = preview.IsImageLoadWithViewSizeEnabled();
```

## Show Placeholders and React to Loading

Image loading is asynchronous by default. `SetPlaceholderUrl()` provides an image to show while the main resource is loading. `ResourceReadySignal()` is emitted with a `Dali::Ui::View` when resources required by the view are ready.

```cpp
class PhotoCell
{
public:
  void Build()
  {
    mImage = Dali::Ui::ImageView::New();

    mImage
      .SetPlaceholderUrl("images/placeholder.png")
      .SetResourceUrl("images/photo.jpg")
      .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

    mImage.ResourceReadySignal().Connect(this, &PhotoCell::OnImageReady);
  }

private:
  void OnImageReady(Dali::Ui::View view)
  {
    Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(view);

    if(imageView && imageView.GetLoadingStatus() == Dali::Ui::Visual::ResourceStatus::READY)
    {
      imageView.SetPlaceholderUrl("");
    }
  }

  Dali::Ui::ImageView mImage;
};
```

You can query the placeholder URL and loading status later.

```cpp
Dali::String placeholder = mImage.GetPlaceholderUrl();
Dali::Ui::Visual::ResourceStatus status = mImage.GetLoadingStatus();
```

## Tint and Crop the Displayed Region

`SetImageColor()` applies a color multiplier to the image. Use it for disabled states, selected states, or monochrome image assets that need theme coloring.

```cpp
Dali::Ui::ImageView icon = Dali::Ui::ImageView::New();

icon
  .SetResourceUrl("images/icons/star.png")
  .SetImageColor(Dali::Ui::UiColor(0xFFD54F))
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

Dali::Ui::UiColor currentColor = icon.GetImageColor();
```

`SetPixelArea()` displays a normalized sub-region of the image as `(x, y, width, height)`. This is useful for sprite sheets, thumbnails, and region previews.

```cpp
Dali::Ui::ImageView sprite = Dali::Ui::ImageView::New();

sprite
  .SetResourceUrl("images/sprites.png")
  .SetPixelArea(Dali::Vector4(0.25f, 0.0f, 0.25f, 0.5f))
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

Dali::Vector4 currentArea = sprite.GetPixelArea();
```

For images authored with pre-multiplied alpha, enable `SetPreMultipliedAlpha()`.

```cpp
Dali::Ui::ImageView badge = Dali::Ui::ImageView::New();

badge
  .SetResourceUrl("images/badge-premultiplied.png")
  .SetPreMultipliedAlpha(true);

bool premultiplied = badge.IsPreMultipliedAlpha();
```

## Apply Alpha Masks

`SetAlphaMaskUrl()` applies the alpha channel from another image to the main image. `SetCropToMask()` controls whether the output is cropped to the mask bounds, and `SetMaskingMode()` chooses whether masking is performed during rendering or loading.

```cpp
Dali::Ui::ImageView avatar = Dali::Ui::ImageView::New();

avatar
  .SetResourceUrl("images/user-photo.jpg")
  .SetAlphaMaskUrl("images/masks/circle.png")
  .SetCropToMask(true)
  .SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING)
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
```

Switch mask behavior by updating the same `ImageView`.

```cpp
void UseBubbleMask(Dali::Ui::ImageView imageView)
{
  imageView
    .SetAlphaMaskUrl("images/masks/bubble.png")
    .SetCropToMask(false)
    .SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_LOADING);

  Dali::String maskUrl = imageView.GetAlphaMaskUrl();
  bool cropToMask = imageView.IsCropToMask();
  Dali::Ui::Image::MaskingType mode = imageView.GetMaskingMode();
}
```

## Control Loading and Cache Lifetime

`SetLoadPolicy()` controls when an image starts loading. Use `Dali::Ui::Image::LoadPolicy::IMMEDIATE` to start loading as soon as the image is configured, or `ATTACHED` to defer loading until the view is attached to the scene.

```cpp
Dali::Ui::ImageView eagerImage = Dali::Ui::ImageView::New();

eagerImage
  .SetResourceUrl("images/banner.jpg")
  .SetLoadPolicy(Dali::Ui::Image::LoadPolicy::IMMEDIATE);
```

`SetReleasePolicy()` controls when cached image resources are released. `DETACHED` releases when the visual is detached from the scene, `DESTROYED` releases when the visual is destroyed, and `NEVER` keeps the image in cache.

```cpp
Dali::Ui::ImageView reusableIcon = Dali::Ui::ImageView::New();

reusableIcon
  .SetResourceUrl("images/icons/settings.png")
  .SetLoadPolicy(Dali::Ui::Image::LoadPolicy::ATTACHED)
  .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::NEVER);
```

Use synchronous loading only when the blocking behavior is acceptable for the interaction being implemented. `SetFastTrackUpload(true)` enables background GPU upload, and `SetOrientationCorrection(true)` applies EXIF orientation metadata automatically.

```cpp
Dali::Ui::ImageView detailImage = Dali::Ui::ImageView::New();

detailImage
  .SetResourceUrl("images/camera-photo.jpg")
  .SetSynchronousLoading(false)
  .SetFastTrackUpload(true)
  .SetOrientationCorrection(true);

bool synchronous = detailImage.IsSynchronousLoading();
bool fastTrack = detailImage.IsFastTrackUploadEnabled();
bool orientationCorrection = detailImage.IsOrientationCorrectionEnabled();
```

## Render Stretchable N-Patch Images

Use `SetNPatchBorder()` for images that must stretch while preserving border regions. The border is specified as `(left, top, right, bottom)` pixel insets. `SetNPatchBorderOnly(true)` renders only the border regions and omits the center.

```cpp
Dali::Ui::ImageView panel = Dali::Ui::ImageView::New();

panel
  .SetResourceUrl("images/panel.9.png")
  .SetNPatchBorder(Dali::Vector4(16.0f, 16.0f, 16.0f, 16.0f))
  .SetNPatchBorderOnly(false);

Dali::Vector4 border = panel.GetNPatchBorder();
bool borderOnly = panel.IsNPatchBorderOnly();
```

When several image visuals exist within the same view, `SetDepthIndex()` changes the image visual render order within the layer.

```cpp
Dali::Ui::ImageView overlay = Dali::Ui::ImageView::New();

overlay
  .SetResourceUrl("images/overlay.png")
  .SetDepthIndex(10);
```

## Property Constants

Prefer typed setters such as `SetResourceUrl()`, `SetFittingMode()`, and `SetPixelArea()` in application code. The `Dali::Ui::ImageView::Property` constants expose the same feature surface for systems that need property indices, such as tooling or property-driven bindings.

`Dali::Ui::ImageView::Property::IMAGE` is backed by `Dali::Ui::ImageViewPropertyIndex::IMAGE` and stores the image URL. The property name is `image`.

```cpp
Dali::Property::Index imageProperty = Dali::Ui::ImageView::Property::IMAGE;
Dali::Property::Index imageIndex = Dali::Ui::ImageViewPropertyIndex::IMAGE;
```

Fitting, sampling, size, and region properties are represented by these constants:

```cpp
Dali::Property::Index fittingMode = Dali::Ui::ImageView::Property::FITTING_MODE;
Dali::Property::Index samplingMode = Dali::Ui::ImageView::Property::SAMPLING_MODE;
Dali::Property::Index desiredWidth = Dali::Ui::ImageView::Property::DESIRED_WIDTH;
Dali::Property::Index desiredHeight = Dali::Ui::ImageView::Property::DESIRED_HEIGHT;
Dali::Property::Index pixelArea = Dali::Ui::ImageView::Property::PIXEL_AREA;
```

Loading and placeholder properties are represented by these constants:

```cpp
Dali::Property::Index placeholder = Dali::Ui::ImageView::Property::PLACEHOLDER_IMAGE;
Dali::Property::Index loadPolicy = Dali::Ui::ImageView::Property::LOAD_POLICY;
Dali::Property::Index releasePolicy = Dali::Ui::ImageView::Property::RELEASE_POLICY;
Dali::Property::Index synchronousLoading = Dali::Ui::ImageView::Property::SYNCHRONOUS_LOADING;
Dali::Property::Index fastTrackUploading = Dali::Ui::ImageView::Property::FAST_TRACK_UPLOADING;
Dali::Property::Index imageLoadWithViewSize = Dali::Ui::ImageView::Property::IMAGE_LOAD_WITH_VIEW_SIZE;
Dali::Property::Index orientationCorrection = Dali::Ui::ImageView::Property::ORIENTATION_CORRECTION;
```

Visual appearance, masking, and N-patch properties are represented by these constants:

```cpp
Dali::Property::Index imageColor = Dali::Ui::ImageView::Property::IMAGE_COLOR;
Dali::Property::Index preMultipliedAlpha = Dali::Ui::ImageView::Property::PRE_MULTIPLIED_ALPHA;
Dali::Property::Index alphaMaskUrl = Dali::Ui::ImageView::Property::ALPHA_MASK_URL;
Dali::Property::Index cropToMask = Dali::Ui::ImageView::Property::CROP_TO_MASK;
Dali::Property::Index maskingMode = Dali::Ui::ImageView::Property::MASKING_MODE;
Dali::Property::Index nPatchBorder = Dali::Ui::ImageView::Property::N_PATCH_BORDER;
Dali::Property::Index nPatchBorderOnly = Dali::Ui::ImageView::Property::N_PATCH_BORDER_ONLY;
```

`Dali::Ui::ImageViewPropertyIndex::PropertyRange` defines the reserved property index range for `ImageView`.

```cpp
Dali::Property::Index start =
  Dali::Ui::ImageViewPropertyIndex::PropertyRange::PROPERTY_START_INDEX;

Dali::Property::Index end =
  Dali::Ui::ImageViewPropertyIndex::PropertyRange::PROPERTY_END_INDEX;
```
