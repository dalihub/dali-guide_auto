---
title: Image View
sidebar_label: Image View
category: views-components
---

# Image View

`Dali::Ui::ImageView` displays an image resource in a dali-ui view tree, with app-facing controls for fitting, sampling, loading, masking, tinting, and N-patch rendering.

## Table of Contents

- [Create and Update an Image](#create-and-update-an-image)
- [Fit, Sample, and Load at a Target Size](#fit-sample-and-load-at-a-target-size)
- [Handle Fallback Image and Loading Completion](#handle-fallback-image-and-loading-completion)
- [Tint and Crop the Displayed Region](#tint-and-crop-the-displayed-region)
- [Apply an Alpha Mask](#apply-an-alpha-mask)
- [Control Loading and Resource Lifetime](#control-loading-and-resource-lifetime)
- [Use N-Patch Images](#use-n-patch-images)
- [Property Constants](#property-constants)

## Create and Update an Image

Create an image view with `Dali::Ui::ImageView::New()`, then assign the displayed resource with `SetResourceUrl()`. The setter returns `Dali::Ui::ImageView&`, so common image setup can be written as a fluent chain.

```cpp
#include <dali-ui-foundation/public-api/image-view.h>

Dali::Ui::ImageView CreateThumbnail()
{
  return Dali::Ui::ImageView::New()
    .SetResourceUrl("images/profile.png")
    .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO)
    .SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX_THEN_LINEAR);
}
```

Use `GetResourceUrl()` when application state needs to know which resource is currently assigned. Use `Reload()` when the same URL should be loaded again, for example after changing loader-time options.

```cpp
void ReplaceImage(Dali::Ui::ImageView imageView, const Dali::String& nextUrl)
{
  if(imageView.GetResourceUrl() != nextUrl)
  {
    imageView.SetResourceUrl(nextUrl);
  }
}

void ReloadCurrentImage(Dali::Ui::ImageView imageView)
{
  imageView.Reload();
}
```

When you receive a generic handle from another dali-ui API, use `Dali::Ui::ImageView::DownCast()` before calling image-specific methods.

```cpp
void ConfigureIfImageView(Dali::BaseHandle handle)
{
  Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(handle);
  if(imageView)
  {
    imageView
      .SetFittingMode(Dali::Ui::Image::FittingMode::CENTER)
      .SetResourceUrl("images/icon.png");
  }
}
```

## Fit, Sample, and Load at a Target Size

`SetFittingMode()` controls how the image is placed inside the view bounds. The available `Dali::Ui::Image::FittingMode` values are `FIT_KEEP_ASPECT_RATIO`, `FILL`, `OVER_FIT_KEEP_ASPECT_RATIO`, and `CENTER`.

```cpp
Dali::Ui::ImageView CreateHeroImage()
{
  return Dali::Ui::ImageView::New()
    .SetResourceUrl("images/hero.jpg")
    .SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);
}

void ToggleContainMode(Dali::Ui::ImageView imageView)
{
  imageView.SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

  Dali::Ui::Image::FittingMode mode = imageView.GetFittingMode();
  (void)mode;
}
```

`SetSamplingMode()` selects the filter used while scaling. For example, `Dali::Ui::Image::SamplingMode::NEAREST` keeps a pixelated look, while `Dali::Ui::Image::SamplingMode::BOX_THEN_LINEAR` is a practical choice for scaled photographic images.

```cpp
void UsePixelArtSampling(Dali::Ui::ImageView imageView)
{
  imageView
    .SetSamplingMode(Dali::Ui::Image::SamplingMode::NEAREST)
    .SetFittingMode(Dali::Ui::Image::FittingMode::CENTER);
}

void UsePhotoSampling(Dali::Ui::ImageView imageView)
{
  imageView.SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX_THEN_LINEAR);

  Dali::Ui::Image::SamplingMode mode = imageView.GetSamplingMode();
  (void)mode;
}
```

`SetDesiredWidth()` and `SetDesiredHeight()` are loader hints in pixels. Set them before loading, or call `Reload()` after changing them for the current resource.

```cpp
void LoadPreviewResolution(Dali::Ui::ImageView imageView)
{
  imageView
    .SetDesiredWidth(640)
    .SetDesiredHeight(360)
    .SetResourceUrl("images/gallery-large.jpg");

  int width  = imageView.GetDesiredWidth();
  int height = imageView.GetDesiredHeight();
  (void)width;
  (void)height;
}

void ChangePreviewResolution(Dali::Ui::ImageView imageView)
{
  imageView
    .SetDesiredWidth(320)
    .SetDesiredHeight(180);

  imageView.Reload();
}
```

`SetImageLoadWithViewSize(true)` tells `ImageView` to use the resolved view size as the image load size. This is useful when a large source image is only ever displayed at a smaller on-screen size.

```cpp
Dali::Ui::ImageView CreateSizedImage()
{
  return Dali::Ui::ImageView::New()
    .SetImageLoadWithViewSize(true)
    .SetFittingMode(Dali::Ui::Image::FittingMode::FILL)
    .SetResourceUrl("images/large-background.jpg");
}
```

## Handle Fallback Image and Loading Completion

Image loading is asynchronous by default. Use `SetPlaceholderUrl()` to show a temporary image while the main resource is loading, and connect `ResourceReadySignal()` to update application state when the image is ready.

`ResourceReadySignal()` uses the `Dali::Ui::View` app-facing object model: callbacks receive the ready `Dali::Ui::View`, not a raw actor.

```cpp
class ImagePresenter : public Dali::ConnectionTracker
{
public:
  Dali::Ui::ImageView Create()
  {
    Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New()
      .SetPlaceholderUrl("images/placeholder.png")
      .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

    imageView.ResourceReadySignal().Connect(this, &ImagePresenter::OnImageReady);
    imageView.SetResourceUrl("images/content.jpg");

    return imageView;
  }

private:
  void OnImageReady(Dali::Ui::View readyView)
  {
    Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(readyView);
    if(imageView)
    {
      Dali::String url = imageView.GetResourceUrl();
      (void)url;
    }
  }
};
```

Use `GetLoadingStatus()` when you need the current `Dali::Ui::Visual::ResourceStatus` value for diagnostics or state synchronization.

```cpp
Dali::Ui::Visual::ResourceStatus ReadImageStatus(Dali::Ui::ImageView imageView)
{
  return imageView.GetLoadingStatus();
}

void UpdatePlaceholder(Dali::Ui::ImageView imageView, const Dali::String& url)
{
  imageView.SetPlaceholderUrl(url);

  Dali::String currentPlaceholder = imageView.GetPlaceholderUrl();
  (void)currentPlaceholder;
}
```

## Tint and Crop the Displayed Region

`SetImageColor()` applies a color multiplier to the image. Use `Dali::Ui::UiColor` for RGBA values or theme color values accepted by your app.

```cpp
Dali::Ui::ImageView CreateDisabledIcon()
{
  return Dali::Ui::ImageView::New()
    .SetResourceUrl("images/action.png")
    .SetImageColor(Dali::Ui::UiColor(1.0f, 1.0f, 1.0f, 0.45f));
}

void RestoreOpaqueImage(Dali::Ui::ImageView imageView)
{
  imageView.SetImageColor(Dali::Ui::UiColor(1.0f, 1.0f, 1.0f, 1.0f));

  Dali::Ui::UiColor color = imageView.GetImageColor();
  (void)color;
}
```

`SetPixelArea()` displays a normalized sub-region of the image as `(x, y, width, height)`, where each component is in the range from `0.0f` to `1.0f`.

```cpp
Dali::Ui::ImageView CreateSpriteFrame()
{
  return Dali::Ui::ImageView::New()
    .SetResourceUrl("images/spritesheet.png")
    .SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.25f, 0.25f))
    .SetFittingMode(Dali::Ui::Image::FittingMode::FILL);
}

void SelectSecondFrame(Dali::Ui::ImageView imageView)
{
  imageView.SetPixelArea(Dali::Vector4(0.25f, 0.0f, 0.25f, 0.25f));

  Dali::Vector4 area = imageView.GetPixelArea();
  (void)area;
}
```

Use `SetPreMultipliedAlpha()` when the source image data should be treated as pre-multiplied alpha.

```cpp
void ConfigureAlpha(Dali::Ui::ImageView imageView)
{
  imageView.SetPreMultipliedAlpha(true);

  bool enabled = imageView.IsPreMultipliedAlpha();
  (void)enabled;
}
```

## Apply an Alpha Mask

`SetAlphaMaskUrl()` applies the alpha channel of another image as a mask. `SetCropToMask()` controls whether the result is cropped to the mask bounds. `SetMaskingMode()` selects whether masking is applied while rendering or while loading.

```cpp
Dali::Ui::ImageView CreateMaskedAvatar()
{
  return Dali::Ui::ImageView::New()
    .SetResourceUrl("images/avatar.jpg")
    .SetAlphaMaskUrl("images/circle-mask.png")
    .SetCropToMask(true)
    .SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING)
    .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
}
```

Switch masking inputs with the typed setters, then read back the active configuration with the matching getters.

```cpp
void UseBubbleMask(Dali::Ui::ImageView imageView)
{
  imageView
    .SetAlphaMaskUrl("images/bubble-mask.png")
    .SetCropToMask(false)
    .SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_LOADING);

  Dali::String maskUrl = imageView.GetAlphaMaskUrl();
  bool cropToMask = imageView.IsCropToMask();
  Dali::Ui::Image::MaskingType mode = imageView.GetMaskingMode();

  (void)maskUrl;
  (void)cropToMask;
  (void)mode;
}
```

## Control Loading and Resource Lifetime

`SetLoadPolicy()` controls when the resource load starts. `Dali::Ui::Image::LoadPolicy::IMMEDIATE` starts loading when configured, while `Dali::Ui::Image::LoadPolicy::ATTACHED` defers loading until the view is attached to the scene.

```cpp
Dali::Ui::ImageView CreateDeferredImage()
{
  return Dali::Ui::ImageView::New()
    .SetLoadPolicy(Dali::Ui::Image::LoadPolicy::ATTACHED)
    .SetResourceUrl("images/deferred.jpg");
}

void UseImmediateLoading(Dali::Ui::ImageView imageView)
{
  imageView.SetLoadPolicy(Dali::Ui::Image::LoadPolicy::IMMEDIATE);

  Dali::Ui::Image::LoadPolicy policy = imageView.GetLoadPolicy();
  (void)policy;
}
```

`SetReleasePolicy()` controls when the image texture is released from cache. Use `DETACHED`, `DESTROYED`, or `NEVER` according to the expected reuse pattern.

```cpp
Dali::Ui::ImageView CreateReusableImage()
{
  return Dali::Ui::ImageView::New()
    .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::NEVER)
    .SetResourceUrl("images/reused-tab-icon.png");
}

void UseDefaultLifetimeForContent(Dali::Ui::ImageView imageView)
{
  imageView.SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DETACHED);

  Dali::Ui::Image::ReleasePolicy policy = imageView.GetReleasePolicy();
  (void)policy;
}
```

`SetSynchronousLoading()` forces synchronous loading on the main thread. `SetFastTrackUpload()` enables fast-track GPU upload. `SetOrientationCorrection()` enables automatic use of EXIF orientation metadata.

```cpp
void ConfigureLoadingFlags(Dali::Ui::ImageView imageView)
{
  imageView
    .SetSynchronousLoading(false)
    .SetFastTrackUpload(true)
    .SetOrientationCorrection(true);

  bool sync = imageView.IsSynchronousLoading();
  bool fast = imageView.IsFastTrackUploadEnabled();
  bool orientation = imageView.IsOrientationCorrectionEnabled();

  (void)sync;
  (void)fast;
  (void)orientation;
}
```

## Use N-Patch Images

Use `SetNPatchBorder()` for scalable image assets where fixed border regions should be preserved. The border is specified as `(left, top, right, bottom)` pixel values. `SetNPatchBorderOnly(true)` renders only the border regions.

```cpp
Dali::Ui::ImageView CreatePanelBackground()
{
  return Dali::Ui::ImageView::New()
    .SetResourceUrl("images/panel.9.png")
    .SetNPatchBorder(Dali::Vector4(12.0f, 12.0f, 12.0f, 12.0f))
    .SetNPatchBorderOnly(false)
    .SetFittingMode(Dali::Ui::Image::FittingMode::FILL);
}

void InspectNPatch(Dali::Ui::ImageView imageView)
{
  Dali::Vector4 border = imageView.GetNPatchBorder();
  bool borderOnly = imageView.IsNPatchBorderOnly();

  (void)border;
  (void)borderOnly;
}
```

Use `SetDepthIndex()` when the image visual needs a specific render ordering among visuals in the same layer.

```cpp
void PlaceOverlayImage(Dali::Ui::ImageView imageView)
{
  imageView.SetDepthIndex(10);
}
```

## Property Constants

For application code, prefer the typed `Dali::Ui::ImageView` setters and getters shown above. The property constants still matter when you need property metadata or a shared property index. `Dali::Ui::ImageView::Property` exposes the public constants for the view, and each one maps to the shared `Dali::Ui::ImageViewPropertyIndex` value.

Common source and appearance constants include `Dali::Ui::ImageView::Property::IMAGE`, `Dali::Ui::ImageView::Property::FITTING_MODE`, `Dali::Ui::ImageView::Property::SAMPLING_MODE`, `Dali::Ui::ImageView::Property::IMAGE_COLOR`, `Dali::Ui::ImageView::Property::PRE_MULTIPLIED_ALPHA`, and `Dali::Ui::ImageView::Property::PIXEL_AREA`.

```cpp
int ImageUrlProperty()
{
  return Dali::Ui::ImageView::Property::IMAGE;
}

int PixelAreaProperty()
{
  return Dali::Ui::ImageViewPropertyIndex::PIXEL_AREA;
}
```

Loading and masking constants include `Dali::Ui::ImageView::Property::PLACEHOLDER_IMAGE`, `Dali::Ui::ImageView::Property::ALPHA_MASK_URL`, `Dali::Ui::ImageView::Property::CROP_TO_MASK`, `Dali::Ui::ImageView::Property::MASKING_MODE`, `Dali::Ui::ImageView::Property::DESIRED_WIDTH`, `Dali::Ui::ImageView::Property::DESIRED_HEIGHT`, `Dali::Ui::ImageView::Property::LOAD_POLICY`, `Dali::Ui::ImageView::Property::RELEASE_POLICY`, `Dali::Ui::ImageView::Property::SYNCHRONOUS_LOADING`, `Dali::Ui::ImageView::Property::IMAGE_LOAD_WITH_VIEW_SIZE`, `Dali::Ui::ImageView::Property::FAST_TRACK_UPLOADING`, and `Dali::Ui::ImageView::Property::ORIENTATION_CORRECTION`.

N-patch constants include `Dali::Ui::ImageView::Property::N_PATCH_BORDER` and `Dali::Ui::ImageView::Property::N_PATCH_BORDER_ONLY`.

```cpp
int FirstImageViewProperty()
{
  return Dali::Ui::ImageViewPropertyIndex::PROPERTY_START_INDEX;
}

int LastReservedImageViewProperty()
{
  return Dali::Ui::ImageViewPropertyIndex::PROPERTY_END_INDEX;
}
```
