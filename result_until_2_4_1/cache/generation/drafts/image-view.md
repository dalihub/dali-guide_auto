---
title: Image View
sidebar_label: Image View
category: views-components
---

# Image View

`Dali::Ui::ImageView` displays an image resource in a dali-ui view tree, with app-facing controls for fitting, sampling, masking, loading, and rendering behavior.

## Table of Contents

- [Create and Set an Image Source](#create-and-set-an-image-source)
- [Fit, Sample, and Load at a Desired Size](#fit-sample-and-load-at-a-desired-size)
- [Tint and Crop the Displayed Image Region](#tint-and-crop-the-displayed-image-region)
- [Use Placeholder Images and Loading Signals](#use-placeholder-images-and-loading-signals)
- [Apply Alpha Masks](#apply-alpha-masks)
- [Tune Loading and Release Behavior](#tune-loading-and-release-behavior)
- [Render N-Patch Borders](#render-n-patch-borders)
- [Property Constants](#property-constants)

## Create and Set an Image Source

Create an initialized `Dali::Ui::ImageView` with `Dali::Ui::ImageView::New()`, then set the image URL with `SetResourceUrl`. The current URL is available through `GetResourceUrl`, and `Reload` reloads the current URL when one has been set.

```cpp
Dali::Ui::ImageView thumbnail = Dali::Ui::ImageView::New();

thumbnail
  .SetResourceUrl("images/profile.png")
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

Dali::String currentUrl = thumbnail.GetResourceUrl();

thumbnail.Reload();
```

Use `Dali::Ui::ImageView::DownCast` when a callback or container gives you a `Dali::Ui::View` and you need `ImageView`-specific APIs.

```cpp
void UpdateImageSource(Dali::Ui::View view, const Dali::String& nextUrl)
{
  Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(view);
  if(imageView)
  {
    imageView.SetResourceUrl(nextUrl);
  }
}
```

## Fit, Sample, and Load at a Desired Size

`SetFittingMode` controls how the image is fitted into the view bounds. Use `Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO` when preserving aspect ratio matters, `Dali::Ui::Image::FittingMode::FILL` when stretching to the available bounds is acceptable, `Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO` when cropping outside the bounds is preferred, and `Dali::Ui::Image::FittingMode::CENTER` when the image should keep its original size.

`SetSamplingMode` controls the filtering used when scaling. `SetDesiredWidth` and `SetDesiredHeight` provide loader hints in pixels, which is useful when a large image is displayed at a smaller size.

```cpp
Dali::Ui::ImageView preview = Dali::Ui::ImageView::New();

preview
  .SetResourceUrl("images/gallery/large-photo.jpg")
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO)
  .SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX_THEN_LINEAR)
  .SetDesiredWidth(480)
  .SetDesiredHeight(320);

Dali::Ui::Image::FittingMode fittingMode = preview.GetFittingMode();
Dali::Ui::Image::SamplingMode samplingMode = preview.GetSamplingMode();
int desiredWidth = preview.GetDesiredWidth();
int desiredHeight = preview.GetDesiredHeight();
```

If the final view size should drive the image load size, enable `SetImageLoadWithViewSize`. This asks the loader to use the resolved view size instead of decoding the full-resolution resource for a smaller display area.

```cpp
Dali::Ui::ImageView cardImage = Dali::Ui::ImageView::New();

cardImage
  .SetResourceUrl("images/card-cover.jpg")
  .SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO)
  .SetImageLoadWithViewSize(true);

bool loadWithViewSize = cardImage.IsImageLoadWithViewSizeEnabled();
```

## Tint and Crop the Displayed Image Region

Use `SetImageColor` to apply a color multiplier to the image. Use `SetPixelArea` to display a normalized sub-region of the source image, expressed as `(x, y, width, height)`.

```cpp
Dali::Ui::ImageView icon = Dali::Ui::ImageView::New();

icon
  .SetResourceUrl("images/icons/search.png")
  .SetImageColor(Dali::Ui::UiColor(1.0f, 1.0f, 1.0f, 0.65f))
  .SetPreMultipliedAlpha(true);

Dali::Ui::UiColor tint = icon.GetImageColor();
bool preMultiplied = icon.IsPreMultipliedAlpha();
```

```cpp
Dali::Ui::ImageView atlasImage = Dali::Ui::ImageView::New();

atlasImage
  .SetResourceUrl("images/sprite-atlas.png")
  .SetPixelArea(Dali::Vector4(0.25f, 0.0f, 0.25f, 0.5f))
  .SetFittingMode(Dali::Ui::Image::FittingMode::FILL);

Dali::Vector4 displayedArea = atlasImage.GetPixelArea();
```

## Use Placeholder Images and Loading Signals

Set `SetPlaceholderUrl` before setting the main resource when you want a temporary image while the main image loads. `ResourceReadySignal` is emitted with a `Dali::Ui::View` parameter when the resource is ready to display, and `GetLoadingStatus` returns the current `Dali::Ui::Visual::ResourceStatus`.

```cpp
class ImageController : public Dali::ConnectionTracker
{
public:
  Dali::Ui::ImageView CreateImage()
  {
    Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New();

    imageView
      .SetPlaceholderUrl("images/placeholder.png")
      .SetResourceUrl("images/remote-photo.jpg")
      .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

    imageView.ResourceReadySignal().Connect(this, &ImageController::OnResourceReady);

    return imageView;
  }

private:
  void OnResourceReady(Dali::Ui::View view)
  {
    Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(view);
    if(imageView)
    {
      Dali::Ui::Visual::ResourceStatus status = imageView.GetLoadingStatus();
      (void)status;
    }
  }
};
```

`GetPlaceholderUrl` lets you inspect the configured placeholder URL.

```cpp
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New();

imageView.SetPlaceholderUrl("images/loading.png");

Dali::String placeholderUrl = imageView.GetPlaceholderUrl();
```

## Apply Alpha Masks

`SetAlphaMaskUrl` applies the alpha channel of a mask image to the main image. `SetCropToMask` controls whether the image is cropped to the mask bounds. `SetMaskingMode` selects whether masking is applied during rendering or during loading.

```cpp
Dali::Ui::ImageView avatar = Dali::Ui::ImageView::New();

avatar
  .SetResourceUrl("images/profile-photo.jpg")
  .SetAlphaMaskUrl("images/masks/circle-mask.png")
  .SetCropToMask(true)
  .SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING)
  .SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);

Dali::String maskUrl = avatar.GetAlphaMaskUrl();
bool cropToMask = avatar.IsCropToMask();
Dali::Ui::Image::MaskingType maskingMode = avatar.GetMaskingMode();
```

Use `Dali::Ui::Image::MaskingType::MASKING_ON_LOADING` when you want the masked result prepared as part of loading.

```cpp
Dali::Ui::ImageView maskedThumbnail = Dali::Ui::ImageView::New();

maskedThumbnail
  .SetResourceUrl("images/thumbnail.jpg")
  .SetAlphaMaskUrl("images/masks/rounded-rect.png")
  .SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_LOADING);
```

## Tune Loading and Release Behavior

`SetLoadPolicy` controls when loading begins. `Dali::Ui::Image::LoadPolicy::IMMEDIATE` loads when the image view is created, while `Dali::Ui::Image::LoadPolicy::ATTACHED` defers loading until the view is attached to the scene.

`SetReleasePolicy` controls when the texture is released from cache. Use `Dali::Ui::Image::ReleasePolicy::DETACHED`, `Dali::Ui::Image::ReleasePolicy::DESTROYED`, or `Dali::Ui::Image::ReleasePolicy::NEVER` according to the lifetime and reuse pattern of the image.

```cpp
Dali::Ui::ImageView reusableImage = Dali::Ui::ImageView::New();

reusableImage
  .SetResourceUrl("images/navigation/background.jpg")
  .SetLoadPolicy(Dali::Ui::Image::LoadPolicy::ATTACHED)
  .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::NEVER)
  .SetFastTrackUpload(true)
  .SetOrientationCorrection(true);

Dali::Ui::Image::LoadPolicy loadPolicy = reusableImage.GetLoadPolicy();
Dali::Ui::Image::ReleasePolicy releasePolicy = reusableImage.GetReleasePolicy();
bool fastTrackUpload = reusableImage.IsFastTrackUploadEnabled();
bool orientationCorrection = reusableImage.IsOrientationCorrectionEnabled();
```

For cases where blocking load behavior is acceptable, `SetSynchronousLoading` enables synchronous image loading.

```cpp
Dali::Ui::ImageView immediateIcon = Dali::Ui::ImageView::New();

immediateIcon
  .SetSynchronousLoading(true)
  .SetResourceUrl("images/icons/local-action.png");

bool synchronous = immediateIcon.IsSynchronousLoading();
```

## Render N-Patch Borders

Use `SetNPatchBorder` for scalable panel images that preserve fixed edge regions. The border is expressed as `(left, top, right, bottom)` pixel values. `SetNPatchBorderOnly` renders only the border regions when the center should be omitted.

```cpp
Dali::Ui::ImageView panel = Dali::Ui::ImageView::New();

panel
  .SetResourceUrl("images/panels/dialog.9.png")
  .SetNPatchBorder(Dali::Vector4(12.0f, 12.0f, 12.0f, 12.0f))
  .SetNPatchBorderOnly(false);

Dali::Vector4 border = panel.GetNPatchBorder();
bool borderOnly = panel.IsNPatchBorderOnly();
```

`SetDepthIndex` adjusts the rendering order of the image visual within the same layer; higher values render above lower values.

```cpp
Dali::Ui::ImageView background = Dali::Ui::ImageView::New();
Dali::Ui::ImageView foreground = Dali::Ui::ImageView::New();

background
  .SetResourceUrl("images/background.png")
  .SetDepthIndex(0);

foreground
  .SetResourceUrl("images/foreground.png")
  .SetDepthIndex(1);
```

## Property Constants

For application code, prefer typed methods such as `SetResourceUrl`, `SetFittingMode`, `SetSamplingMode`, `SetAlphaMaskUrl`, and `SetReleasePolicy`. The property constants are owned by `Dali::Ui::ImageView::Property` and mirror the `Dali::Ui::ImageViewPropertyIndex` definitions for systems that need property metadata.

| Property constant | Purpose |
| --- | --- |
| `Dali::Ui::ImageView::Property::IMAGE` | Image resource URL |
| `Dali::Ui::ImageView::Property::FITTING_MODE` | `Dali::Ui::Image::FittingMode` value |
| `Dali::Ui::ImageView::Property::SAMPLING_MODE` | `Dali::Ui::Image::SamplingMode` value |
| `Dali::Ui::ImageView::Property::DESIRED_WIDTH` | Desired loader width in pixels |
| `Dali::Ui::ImageView::Property::DESIRED_HEIGHT` | Desired loader height in pixels |
| `Dali::Ui::ImageView::Property::IMAGE_COLOR` | Image color multiplier |
| `Dali::Ui::ImageView::Property::PRE_MULTIPLIED_ALPHA` | Pre-multiplied alpha flag |
| `Dali::Ui::ImageView::Property::PLACEHOLDER_IMAGE` | Placeholder image URL |
| `Dali::Ui::ImageView::Property::IMAGE_LOAD_WITH_VIEW_SIZE` | View-size-driven loading flag |
| `Dali::Ui::ImageView::Property::ALPHA_MASK_URL` | Alpha mask image URL |
| `Dali::Ui::ImageView::Property::CROP_TO_MASK` | Crop-to-mask flag |
| `Dali::Ui::ImageView::Property::MASKING_MODE` | `Dali::Ui::Image::MaskingType` value |
| `Dali::Ui::ImageView::Property::LOAD_POLICY` | `Dali::Ui::Image::LoadPolicy` value |
| `Dali::Ui::ImageView::Property::RELEASE_POLICY` | `Dali::Ui::Image::ReleasePolicy` value |
| `Dali::Ui::ImageView::Property::SYNCHRONOUS_LOADING` | Synchronous loading flag |
| `Dali::Ui::ImageView::Property::FAST_TRACK_UPLOADING` | Fast-track GPU upload flag |
| `Dali::Ui::ImageView::Property::ORIENTATION_CORRECTION` | EXIF orientation correction flag |
| `Dali::Ui::ImageView::Property::N_PATCH_BORDER` | N-patch border insets |
| `Dali::Ui::ImageView::Property::N_PATCH_BORDER_ONLY` | N-patch border-only rendering flag |
| `Dali::Ui::ImageView::Property::PIXEL_AREA` | Normalized displayed image sub-region |

`Dali::Ui::ImageViewPropertyIndex::PropertyRange` defines the property index range for `ImageView`, and members such as `Dali::Ui::ImageViewPropertyIndex::IMAGE`, `Dali::Ui::ImageViewPropertyIndex::FITTING_MODE`, `Dali::Ui::ImageViewPropertyIndex::PLACEHOLDER_IMAGE`, and `Dali::Ui::ImageViewPropertyIndex::PIXEL_AREA` are the underlying index definitions used by `Dali::Ui::ImageView::Property`.
