---
id: image-view
title: "image-view"
sidebar_label: "image-view"
---
## Overview

`ImageView` is a UI component used to display static [image](./image.md) resources like PNG, JPEG, and SVG files within your application layout. It provides a robust set of properties to manage how content is rendered, scaled, and synchronized with the lifecycle of your application views.

## Basic Image Loading

The [core](./core.md) functionality of this module revolves around assigning a source URL to display an [image](./image.md). By utilizing the provided factory methods, you can instantiate the component and define the resource path in a single, concise statement.

```cpp
ImageView imageView = ImageView::New("image.png");
imageView.SetResourceUrl("image.png");
```

You can also update the image source dynamically at runtime. If a previous image was set, changing the URL will automatically trigger an update to the visual content.

```cpp
imageView.SetResourceUrl("new_image.png");
```

To improve the perceived performance during network or disk I/O, you can assign a placeholder image. The placeholder is displayed immediately while the primary resource is being fetched or prepared.

```cpp
ImageView imageView = ImageView::New();
imageView.SetPlaceholderUrl("placeholder.png");
```

## Layout and Scaling Control

Managing how an image occupies its bounding box is critical for maintaining aspect ratios and UI consistency. You can influence the final visual output by selecting an appropriate fitting mode and fine-tuning the sampling behavior.

```cpp
ImageView imageView = ImageView::New();
imageView.SetFittingMode(Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
imageView.SetSamplingMode(Image::SamplingMode::LINEAR);
```

When working with large assets, it is often beneficial to provide a hint to the loader regarding the dimensions you intend to use. Setting desired dimensions helps optimize memory usage by limiting the decoded size of the image.

```cpp
ImageView imageView = ImageView::New();
imageView.SetDesiredWidth(200);
imageView.SetDesiredHeight(200);
```

## Advanced Resource Management

The framework allows for granular control over when an image is loaded into memory and how long it remains in the cache. By selecting the appropriate policy, you can balance memory efficiency against the need for rapid visual updates.

```cpp
ImageView imageView = ImageView::New();
imageView.SetLoadPolicy(Image::LoadPolicy::IMMEDIATE);
imageView.SetReleasePolicy(Image::ReleasePolicy::DESTROYED);
```

In scenarios where you need an image to be ready for display before proceeding with further logic, you can force the system to perform a synchronous load. Additionally, for critical paths that require high-speed updates, you can enable fast-track uploading.

```cpp
ImageView imageView = ImageView::New();
imageView.SetSynchronousLoading(true);
imageView.SetFastTrackUpload(true);
```

## Visual Adjustments and Effects

Beyond basic placement, you can apply non-destructive visual adjustments to the rendered image. These include applying a color tint, which modulates the image's appearance, and enabling pre-multiplied alpha for specific compositing requirements.

```cpp
ImageView imageView = ImageView::New();
imageView.SetImageColor(UiColor(1.0f, 0.5f, 0.5f, 1.0f));
imageView.SetPreMultipliedAlpha(true);
```

For assets containing orientation metadata, the framework can automatically rotate the image to match the EXIF data.

```cpp
ImageView imageView = ImageView::New();
imageView.SetOrientationCorrection(true);
```

## Masking and Cropping

You can define custom visibility boundaries for your images by providing an alpha mask. This is useful for creating non-rectangular UI elements such as circular user avatars.

```cpp
ImageView imageView = ImageView::New();
imageView.SetAlphaMaskUrl("mask.png");
imageView.SetCropToMask(true);
```

When using masks, the system allows you to specify whether the masking process should occur during the initial loading phase or the final rendering phase.

```cpp
ImageView imageView = ImageView::New();
imageView.SetMaskingMode(Image::MaskingType::MASKING_ON_RENDERING);
```

## Handling Nine-Patch Assets

Nine-patch images are supported to allow for scalable UI backgrounds that maintain corner integrity. You can define the stretchable regions by setting the N-patch border insets.

```cpp
ImageView imageView = ImageView::New();
imageView.SetNPatchBorder(Vector4(10.0f, 10.0f, 10.0f, 10.0f));
imageView.SetNPatchBorderOnly(true);
```

## Event Handling and Status Monitoring

Monitoring the status of your assets is essential for building reactive interfaces. You can query the current loading status of the image at any time.

```cpp
if(imageView.GetLoadingStatus() == Visual::ResourceStatus::READY)
{
  // Image is ready
}
```

For more complex workflows, the system provides a signal that notifies your application when the resource is fully loaded and ready to be displayed.

```cpp
class MyHandler
{
public:
  void OnReady(ImageView& view)
  {
  }
};
// Inside setup code:
imageView.ResourceReadySignal().Connect(myHandlerInstance, &MyHandler::OnReady);
```

## Related Sub-Components

*   `[image-view-properties](./image-view-properties.md)`: Provides a comprehensive listing of all available properties for configuring the visual state and behavior of your [image](./image.md) assets. → See: [[image-view-properties](./image-view-properties.md)]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/image-view)
