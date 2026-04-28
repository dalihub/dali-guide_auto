---
id: image-view
title: "image-view"
sidebar_label: "image-view"
---
# Overview

`ImageView` is a specialized UI component used to display static image resources like PNG, JPEG, and SVG files within your application layout. It provides a robust interface for [rendering](./rendering.md) image content while managing memory, layout, and visual fidelity through various configuration properties.

# Loading and Resource Management

Configuring how [images](./images.md) are fetched, cached, and released is essential for optimizing application memory and performance. You can define the resource location using the `SetResourceUrl(const Dali::String &)`. To improve user experience during initial loading, it is recommended to provide a fallback image via `SetPlaceholderUrl(const Dali::String &)`.

The timing of image loading is controlled by the load policy, which can be set to either `LoadPolicy::IMMEDIATE` to load upon [object](./object.md) creation, or `LoadPolicy::ATTACHED` to delay loading until the view is attached to the [scene](./scene.md). Additionally, you may define when the image is evicted from the cache using policies such as `ReleasePolicy::DESTROYED`, `ReleasePolicy::DETACHED`, or `ReleasePolicy::NEVER`. For scenarios requiring high-responsiveness, you can enable `SetSynchronousLoading(bool)` or `SetFastTrackUpload(bool)` to minimize latency.

```cpp
ImageView imageView = ImageView::New();
imageView.SetResourceUrl("image.png");
imageView.SetPlaceholderUrl("placeholder.png");
imageView.SetLoadPolicy(LoadPolicy::ATTACHED);
```

# Display and Visual Configuration

The visual output of the image can be finely tuned to match the application's aesthetic requirements. You can apply a color tint to the image using the `SetImageColor(const UiColor &)`. If you only need to display a specific segment of the source image, the `SetPixelArea(const Vector4 &)` allows you to define a rectangular sub-region for rendering.

When images are processed for display, you may enable `SetOrientationCorrection(bool)` to automatically respect EXIF rotation metadata embedded in the source file. For advanced transparency handling, the `SetPreMultipliedAlpha(bool)` can be used to toggle whether the source colors are pre-multiplied by their alpha channel.

```cpp
ImageView imageView = ImageView::New();
imageView.SetImageColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f));
imageView.SetOrientationCorrection(true);
```

# Layout and Sizing

Ensuring an image fits correctly within its container is handled through sizing hints and fitting behaviors. You can provide rasterization hints to the loader by setting the width and height with the `SetDesiredWidth(int)` and `SetDesiredHeight(int)`. These hints help optimize memory usage by allowing the loader to decode the image at an appropriate resolution.

To determine how an image scales relative to its assigned view dimensions, use the `SetFittingMode(FittingMode::Type)` with values such as `FittingMode::FIT_KEEP_ASPECT_RATIO` or `FittingMode::FILL`. If you want the image to match the container's size automatically during loading, you can invoke the `SetImageLoadWithViewSize(bool)`.

```cpp
ImageView imageView = ImageView::New();
imageView.SetDesiredWidth(200);
imageView.SetDesiredHeight(200);
imageView.SetFittingMode(FittingMode::FILL);
```

# Masking and Specialized Rendering

ImageView supports complex rendering techniques such as alpha masking and N-Patch scaling for dynamic UI components. An alpha mask can be applied via the `SetAlphaMaskUrl(const Dali::String &)`, which enables masking modes like `MaskingType::MASKING_ON_RENDERING` or `MaskingType::MASKING_ON_LOADING`. The `SetCropToMask(bool)` provides control over whether the rendered result respects the mask bounds.

For flexible UI elements that must scale without distorting borders, you can configure the N-patch behavior using the `SetNPatchBorder(const Vector4 &)`. Additionally, the `SetNPatchBorderOnly(bool)` allows for rendering only the border regions of an N-patch resource.

```cpp
ImageView imageView = ImageView::New();
imageView.SetAlphaMaskUrl("mask.png");
imageView.SetNPatchBorder(Vector4(10.0f, 10.0f, 10.0f, 10.0f));
imageView.SetNPatchBorderOnly(true);
```

# Handling Image Readiness

Monitoring the lifecycle of an image is crucial for triggering UI updates once content is available. You can query the current state using the `GetLoadingStatus()`, which returns statuses like `Visual::ResourceStatus::READY` or `Visual::ResourceStatus::FAILED`. For asynchronous workflows, connect to the `ResourceReadySignal()` to execute logic immediately upon successful image loading.

```cpp
ImageView imageView = ImageView::New();
imageView.ResourceReadySignal().Connect(this, &MyHandler::OnResourceReady);
```

## Related Sub-Components

*   `[image-view-properties](./image-view-properties.md)`: Provides the detailed indices and descriptions for all properties configurable on the view. → See: [[image-view-properties](./image-view-properties.md)]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/image-view)
