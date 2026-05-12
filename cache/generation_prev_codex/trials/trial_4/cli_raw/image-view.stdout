---
title: Image View
sidebar_label: Image View
category: views-components
---

# Image View

`Dali::Ui::ImageView` displays image resources in a dali-ui application using the `Dali::Ui::View` object model.

## Table of Contents

- [Create and Configure an Image View](#create-and-configure-an-image-view)
- [Fit, Crop, and Sample Image Content](#fit-crop-and-sample-image-content)
- [Use Temporary Images and Loading State](#use-temporary-images-and-loading-state)
- [Apply Color and Alpha Masks](#apply-color-and-alpha-masks)
- [Control Resource Loading Policy](#control-resource-loading-policy)
- [Use N-Patch Image Borders](#use-n-patch-image-borders)
- [Work with Property Identifiers](#work-with-property-identifiers)

## Create and Configure an Image View

Use `Dali::Ui::ImageView::New` to create an image view, then configure it with typed setters. In app code, treat the image view as a dali-ui view object rather than as a raw actor.

```cpp
using namespace Dali;
using namespace Dali::Ui;

ImageView CreateThumbnail(const Dali::String& imageUrl)
{
  ImageView imageView = ImageView::New();

  imageView.SetResourceUrl(imageUrl)
           .SetDesiredWidth(160)
           .SetDesiredHeight(120)
           .SetImageLoadWithViewSize(true)
           .SetOrientationCorrection(true);

  return imageView;
}
```

`Dali::Ui::ImageView` keeps the image resource URL, desired loading size, and orientation behavior as part of the image view feature surface.

```cpp
void InspectImageView(const ImageView& imageView)
{
  Dali::String url         = imageView.GetResourceUrl();
  int          width       = imageView.GetDesiredWidth();
  int          height      = imageView.GetDesiredHeight();
  bool         viewSized   = imageView.IsImageLoadWithViewSizeEnabled();
  bool         orientation = imageView.IsOrientationCorrectionEnabled();

  (void)url;
  (void)width;
  (void)height;
  (void)viewSized;
  (void)orientation;
}
```

When a handle comes from generic dali-ui code, use `Dali::Ui::ImageView::DownCast` before using image-specific APIs.

```cpp
ImageView ResolveImageView(BaseHandle handle)
{
  ImageView imageView = ImageView::DownCast(handle);

  if(imageView)
  {
    imageView.SetResourceUrl("images/profile.png");
  }

  return imageView;
}
```

## Fit, Crop, and Sample Image Content

`Dali::Ui::ImageView` separates image resource selection from presentation. Use `Dali::Ui::ImageView::SetFittingMode` to control how the image fits the view, `Dali::Ui::ImageView::SetPixelArea` to select part of the source, and `Dali::Ui::ImageView::SetSamplingMode` to control sampling behavior.

```cpp
ImageView CreateCroppedImage(const Dali::String& imageUrl,
                             Image::FittingMode fittingMode,
                             Image::SamplingMode samplingMode)
{
  ImageView imageView = ImageView::New();

  imageView.SetResourceUrl(imageUrl)
           .SetFittingMode(fittingMode)
           .SetSamplingMode(samplingMode)
           .SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f));

  return imageView;
}
```

You can read the active presentation settings back from the image view when synchronizing UI state.

```cpp
void ReadPresentationState(const ImageView& imageView)
{
  Image::FittingMode  fittingMode  = imageView.GetFittingMode();
  Image::SamplingMode samplingMode = imageView.GetSamplingMode();
  Vector4             pixelArea    = imageView.GetPixelArea();

  (void)fittingMode;
  (void)samplingMode;
  (void)pixelArea;
}
```

For image assets where decode size matters, combine `Dali::Ui::ImageView::SetDesiredWidth`, `Dali::Ui::ImageView::SetDesiredHeight`, and `Dali::Ui::ImageView::SetImageLoadWithViewSize`.

```cpp
void ConfigureDecodeSize(ImageView imageView, int width, int height)
{
  imageView.SetDesiredWidth(width)
           .SetDesiredHeight(height)
           .SetImageLoadWithViewSize(true)
           .Reload();
}
```

## Use Temporary Images and Loading State

Use `Dali::Ui::ImageView::SetPlaceholderUrl` to display a temporary image while the main resource is loading. `Dali::Ui::ImageView::GetLoadingStatus` reports the current visual resource status.

```cpp
ImageView CreateRemoteImage(const Dali::String& imageUrl,
                            const Dali::String& temporaryUrl)
{
  ImageView imageView = ImageView::New();

  imageView.SetPlaceholderUrl(temporaryUrl)
           .SetResourceUrl(imageUrl)
           .SetFastTrackUpload(true);

  return imageView;
}
```

`Dali::Ui::ImageView::ResourceReadySignal` is the image view signal for reacting when the resource becomes ready.

```cpp
class ImageController
{
public:
  void Configure(ImageView imageView)
  {
    mImageView = imageView;

    mImageView.ResourceReadySignal().Connect(this, &ImageController::OnResourceReady);
  }

  void OnResourceReady(ImageView imageView)
  {
    Ui::Visual::ResourceStatus status = imageView.GetLoadingStatus();
    Dali::String               url    = imageView.GetResourceUrl();

    (void)status;
    (void)url;
  }

private:
  ImageView mImageView;
};
```

The temporary image URL is owned by `Dali::Ui::ImageView` and can be inspected when updating or rebuilding a view.

```cpp
void ReplaceImage(ImageView imageView, const Dali::String& nextUrl)
{
  Dali::String temporaryUrl = imageView.GetPlaceholderUrl();

  imageView.SetResourceUrl(nextUrl)
           .Reload();

  (void)temporaryUrl;
}
```

## Apply Color and Alpha Masks

Use `Dali::Ui::ImageView::SetImageColor` to tint image content. This belongs to `Dali::Ui::ImageView`, so prefer the typed image-view API in application code.

```cpp
ImageView CreateTintedIcon(const Dali::String& iconUrl, const UiColor& color)
{
  ImageView imageView = ImageView::New();

  imageView.SetResourceUrl(iconUrl)
           .SetImageColor(color)
           .SetPreMultipliedAlpha(true);

  return imageView;
}
```

Use `Dali::Ui::ImageView::SetAlphaMaskUrl`, `Dali::Ui::ImageView::SetMaskingMode`, and `Dali::Ui::ImageView::SetCropToMask` when an image should be shaped by an alpha mask.

```cpp
ImageView CreateMaskedAvatar(const Dali::String& imageUrl,
                             const Dali::String& maskUrl,
                             Image::MaskingType maskingMode)
{
  ImageView imageView = ImageView::New();

  imageView.SetResourceUrl(imageUrl)
           .SetAlphaMaskUrl(maskUrl)
           .SetMaskingMode(maskingMode)
           .SetCropToMask(true);

  return imageView;
}
```

You can inspect the active masking configuration through the corresponding getters.

```cpp
void ReadMaskState(const ImageView& imageView)
{
  Dali::String       maskUrl     = imageView.GetAlphaMaskUrl();
  Image::MaskingType maskingMode = imageView.GetMaskingMode();
  bool               cropToMask  = imageView.IsCropToMask();
  UiColor            imageColor  = imageView.GetImageColor();
  bool               premultiply = imageView.IsPreMultipliedAlpha();

  (void)maskUrl;
  (void)maskingMode;
  (void)cropToMask;
  (void)imageColor;
  (void)premultiply;
}
```

## Control Resource Loading Policy

`Dali::Ui::ImageView` exposes loading and release policy controls for image-heavy views. Use typed setters so the intent stays local to the image view.

```cpp
ImageView CreatePolicyControlledImage(const Dali::String& imageUrl,
                                      Image::LoadPolicy loadPolicy,
                                      Image::ReleasePolicy releasePolicy)
{
  ImageView imageView = ImageView::New();

  imageView.SetResourceUrl(imageUrl)
           .SetLoadPolicy(loadPolicy)
           .SetReleasePolicy(releasePolicy)
           .SetSynchronousLoading(false)
           .SetFastTrackUpload(true);

  return imageView;
}
```

For diagnostics or view recycling logic, read the active policy values from the same object.

```cpp
void ReadLoadingPolicy(const ImageView& imageView)
{
  Image::LoadPolicy    loadPolicy    = imageView.GetLoadPolicy();
  Image::ReleasePolicy releasePolicy = imageView.GetReleasePolicy();
  bool                 synchronous   = imageView.IsSynchronousLoading();
  bool                 fastTrack     = imageView.IsFastTrackUploadEnabled();

  (void)loadPolicy;
  (void)releasePolicy;
  (void)synchronous;
  (void)fastTrack;
}
```

Call `Dali::Ui::ImageView::Reload` after changing image-loading options that should be applied to the current resource.

```cpp
void ReconfigureLoading(ImageView imageView,
                        Image::LoadPolicy loadPolicy,
                        Image::ReleasePolicy releasePolicy)
{
  imageView.SetLoadPolicy(loadPolicy)
           .SetReleasePolicy(releasePolicy)
           .Reload();
}
```

## Use N-Patch Image Borders

For scalable image assets, configure N-patch behavior directly on `Dali::Ui::ImageView`.

```cpp
ImageView CreateNPatchPanel(const Dali::String& panelUrl)
{
  ImageView imageView = ImageView::New();

  imageView.SetResourceUrl(panelUrl)
           .SetNPatchBorder(Vector4(12.0f, 12.0f, 12.0f, 12.0f))
           .SetNPatchBorderOnly(false);

  return imageView;
}
```

`Dali::Ui::ImageView::GetNPatchBorder` and `Dali::Ui::ImageView::IsNPatchBorderOnly` expose the active N-patch configuration.

```cpp
void ReadNPatchState(const ImageView& imageView)
{
  Vector4 border     = imageView.GetNPatchBorder();
  bool    borderOnly = imageView.IsNPatchBorderOnly();

  (void)border;
  (void)borderOnly;
}
```

Use `Dali::Ui::ImageView::SetDepthIndex` when ordering image-view visuals inside a composed dali-ui view tree.

```cpp
ImageView CreateLayeredImage(const Dali::String& imageUrl, int depthIndex)
{
  ImageView imageView = ImageView::New();

  imageView.SetResourceUrl(imageUrl)
           .SetDepthIndex(depthIndex);

  return imageView;
}
```

## Work with Property Identifiers

Most application code should use the typed `Dali::Ui::ImageView` setters and getters. Property identifiers are still part of the image view feature surface and are useful when integrating with property-driven tooling.

`Dali::Ui::ImageView::Property` owns the image-view property names:

```cpp
void ReferenceImageViewProperties()
{
  auto imageProperty       = ImageView::Property::IMAGE;
  auto fittingModeProperty = ImageView::Property::FITTING_MODE;
  auto pixelAreaProperty   = ImageView::Property::PIXEL_AREA;
  auto temporaryProperty   = ImageView::Property::PLACEHOLDER_IMAGE;
  auto maskProperty        = ImageView::Property::ALPHA_MASK_URL;
  auto colorProperty       = ImageView::Property::IMAGE_COLOR;

  (void)imageProperty;
  (void)fittingModeProperty;
  (void)pixelAreaProperty;
  (void)temporaryProperty;
  (void)maskProperty;
  (void)colorProperty;
}
```

`Dali::Ui::ImageViewPropertyIndex` provides the corresponding image-view property index constants, including `Dali::Ui::ImageViewPropertyIndex::PropertyRange`.

```cpp
void ReferenceImageViewPropertyIndexes()
{
  auto firstImageViewProperty = ImageViewPropertyIndex::PropertyRange;
  auto imageIndex             = ImageViewPropertyIndex::IMAGE;
  auto desiredWidthIndex      = ImageViewPropertyIndex::DESIRED_WIDTH;
  auto desiredHeightIndex     = ImageViewPropertyIndex::DESIRED_HEIGHT;
  auto loadingIndex           = ImageViewPropertyIndex::FAST_TRACK_UPLOADING;
  auto maskIndex              = ImageViewPropertyIndex::MASKING_MODE;

  (void)firstImageViewProperty;
  (void)imageIndex;
  (void)desiredWidthIndex;
  (void)desiredHeightIndex;
  (void)loadingIndex;
  (void)maskIndex;
}
```

For transform or opacity animation on a dali-ui view, inherited property enums such as `Dali::Actor::Property::OPACITY`, `Dali::Actor::Property::POSITION`, and `Dali::Actor::Property::SCALE` are owned by `Dali::Actor::Property`; use them only as property identifiers while keeping the animated object in the `Dali::Ui::View` object model.
