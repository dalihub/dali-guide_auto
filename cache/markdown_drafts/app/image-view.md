## Overview

`ImageView` is a UI component used to display static image resources like PNG, JPEG, and SVG within a Dali-based application. It provides a robust set of properties to manage how images are retrieved, sized, and rendered, serving as the standard container for static visual assets.

```cpp
ImageView imageView = ImageView::New();
imageView.SetResourceUrl("image.png");
```

## Resource Management and Loading

Configure source URLs, loading policies, and synchronous vs. asynchronous behavior to optimize how images are fetched and managed in memory. The primary resource is defined using the `SetResourceUrl(const Dali::String &)`, while a temporary fallback can be defined via the `SetPlaceholderUrl(const Dali::String &)`.

The timing of the fetch is determined by the `SetLoadPolicy(LoadPolicy::Type)`. You can choose to initiate loading immediately upon creation using the `LoadPolicy::IMMEDIATE` or wait until the view is attached to the scene using the `LoadPolicy::ATTACHED`. Furthermore, the lifetime of the image in memory is governed by the `SetReleasePolicy(ReleasePolicy::Type)`, which allows resources to be freed when the view is detached, destroyed, or persisted in the cache using the `ReleasePolicy::DETACHED, ReleasePolicy::DESTROYED, ReleasePolicy::NEVER`. For debugging or immediate processing, you may force synchronous behavior using the `SetSynchronousLoading(bool)`.

```cpp
ImageView imageView = ImageView::New();
imageView.SetLoadPolicy(LoadPolicy::IMMEDIATE);
imageView.SetReleasePolicy(ReleasePolicy::DESTROYED);
imageView.SetSynchronousLoading(true);
```

## Visual Configuration and Styling

Apply color tints, coordinate alpha blending, and manage image orientation to ensure visual consistency across your application. The overall appearance of the image can be modified using the `SetImageColor(const UiColor &)`, which acts as a color multiplier applied to the rendered output. 

When handling semi-transparent assets, it is important to specify whether the source data uses pre-multiplied alpha via the `SetPreMultipliedAlpha(bool)`. Additionally, if you are working with images that contain EXIF orientation metadata, the `SetOrientationCorrection(bool)` ensures that the image is rotated automatically to match its intended orientation.

```cpp
ImageView imageView = ImageView::New();
imageView.SetImageColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f));
imageView.SetOrientationCorrection(true);
```

## Layout, Scaling, and Cropping

Control how images fit into the UI container using sampling modes, desired dimensions, and pixel-area clipping. When an image's natural size differs from the container size, the `SetFittingMode(FittingMode::Type)` determines the scaling behavior, such as using the `FittingMode::FIT_KEEP_ASPECT_RATIO` to maintain proportions. 

For finer control over resolution, you can suggest specific dimensions to the loader using the `SetDesiredWidth(int)` and `SetDesiredHeight(int)`. If you wish to display only a portion of the source image, the `SetPixelArea(const Vector4 &)` accepts a vector defining the normalized sub-region. You can also influence the interpolation quality during scaling by utilizing the `SetSamplingMode(SamplingMode::Type)`.

```cpp
ImageView imageView = ImageView::New();
imageView.SetFittingMode(FittingMode::FIT_KEEP_ASPECT_RATIO);
imageView.SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f));
```

## Masking and N-Patch Support

Implement specialized visual effects using alpha masks or handle scalable UI assets with N-patch border definitions. An alpha mask can be applied via the `SetAlphaMaskUrl(const Dali::String &)`, and you can further refine this effect by toggling the `SetCropToMask(bool)`. The timing of this operation is managed by the `SetMaskingMode(MaskingType::Type)`, which supports either the `MaskingType::MASKING_ON_LOADING` or the `MaskingType::MASKING_ON_RENDERING`.

For UI elements that must stretch without distorting their corners, you can define an N-patch border using the `SetNPatchBorder(const Vector4 &)`. If you only intend to render the border regions without the center content, use the `SetNPatchBorderOnly(bool)`.

```cpp
ImageView imageView = ImageView::New();
imageView.SetAlphaMaskUrl("mask.png");
imageView.SetNPatchBorder(Vector4(10.0f, 10.0f, 10.0f, 10.0f));
imageView.SetNPatchBorderOnly(true);
```

## Handling Image Readiness

Monitor the loading state of your assets using signals to trigger UI updates once the image resource is fully prepared for display. The `ResourceReadySignal()` allows you to connect a handler that triggers when the loading operation completes. You can also poll the current state of the resource at any time using the `GetLoadingStatus()`, which returns values such as `Visual::ResourceStatus::READY` or `Visual::ResourceStatus::FAILED`.

```cpp
class MyHandler
{
public:
  void OnReady(ImageView& view) { }
};

MyHandler handler;
ImageView imageView = ImageView::New();
imageView.ResourceReadySignal().Connect(&handler, &MyHandler::OnReady);
```

## Performance Optimization

Utilize advanced settings like FastTrack upload and view-size-aware loading to maintain high frame rates and efficient memory usage. Enabling `SetImageLoadWithViewSize(bool)` instructs the system to load the image at a resolution optimized for the current view size, reducing memory overhead for high-resolution assets. For scenarios where rapid texture updates are required, use the `SetFastTrackUpload(bool)` to prioritize the upload process to the GPU.

```cpp
ImageView imageView = ImageView::New();
imageView.SetImageLoadWithViewSize(true);
imageView.SetFastTrackUpload(true);
```

## Related Sub-Components

*   **image-view-properties**: Contains the specific property indices and definitions used for configuring ImageView via property-based accessors. → See: [image-view-properties]