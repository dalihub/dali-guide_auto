---
title: Image View
sidebar_label: Image View
category: views-components
---

# Image View

`ImageView` is a core component for displaying visual assets, supporting a wide range of image formats, remote URLs, and sophisticated rendering effects such as alpha masking and N-Patch stretching.

## Table of Contents

- [Displaying an Image](#displaying-an-image)
- [Fitting and Sizing](#fitting-and-sizing)
- [Alpha Masking and Effects](#alpha-masking-and-effects)
- [Loading and Performance Policies](#loading-and-performance-policies)
- [N-Patch Support](#n-patch-support)
- [Monitoring Load Status](#monitoring-load-status)

## Displaying an Image

To display an image, create a `Dali::Ui::ImageView` and provide a resource URL. This URL can point to a local file path or a remote location. Since `ImageView` inherits from `Dali::Ui::View`, it integrates seamlessly into the application's view hierarchy.

```cpp
using namespace Dali::Ui;

// Create an ImageView and set its resource
auto imageView = ImageView::New();
imageView.SetResourceUrl("images/background.png")
         .SetProperty(Dali::Actor::Property::PARENT_ORIGIN, ParentOrigin::CENTER)
         .SetProperty(Dali::Actor::Property::PIVOT, AnchorPoint::CENTER);

// Add to a parent View
rootView.Add(imageView);
```

## Fitting and Sizing

`ImageView` provides several ways to control how the source image is mapped to the view's dimensions.

### Fitting Modes
The `SetFittingMode` method defines how the image scales to fit the view. Common modes include fitting to width, height, or filling the entire area while maintaining aspect ratio.

```cpp
// Ensure the image fills the view while keeping its aspect ratio
imageView.SetFittingMode(Dali::FittingMode::FIT_WIDTH);
```

### Desired Resolution and Sampling
For performance optimization, you can set the `DESIRED_WIDTH` and `DESIRED_HEIGHT`. This hints the loader to downscale the image during the loading phase, significantly reducing memory consumption for large assets.

```cpp
imageView.SetDesiredWidth(128)
         .SetDesiredHeight(128)
         .SetSamplingMode(Dali::SamplingMode::BOX_THEN_LINEAR);
```

### Pixel Area
If you only need to display a portion of an image, use `SetPixelArea`. The coordinates are normalized (0.0 to 1.0).

```cpp
// Display only the center 50% of the image
imageView.SetPixelArea(Vector4(0.25f, 0.25f, 0.5f, 0.5f));
```

## Alpha Masking and Effects

`ImageView` supports complex visual modifications like alpha masking and colorization without requiring custom shaders.

### Alpha Masking
Apply a transparency mask using another image URL. You can also toggle `SetCropToMask` to determine if the view bounds should shrink to the mask's non-transparent area.

```cpp
imageView.SetAlphaMaskUrl("images/mask_circle.png")
         .SetMaskingMode(Dali::Ui::Visual::MaskingMode::MASKING_ON_RENDERING)
         .SetCropToMask(true);
```

### Image Colorization
You can apply a uniform color tint to the image using `SetImageColor`.

```cpp
// Apply a blue tint to the image
imageView.SetImageColor(UiColor(0.0f, 0.5f, 1.0f, 1.0f));
```

## Loading and Performance Policies

Managing how and when images are loaded is critical for smooth UI performance, especially in list views or resource-heavy scenes.

### Load and Release Policies
Use `SetLoadPolicy` to control when the image fetching starts and `SetReleasePolicy` to manage memory when the view is detached from the scene.

```cpp
imageView.SetLoadPolicy(Dali::Ui::Visual::LoadPolicy::IMMEDIATE)
         .SetReleasePolicy(Dali::Ui::Visual::ReleasePolicy::DETACHED);
```

### Initial Images and Synchronous Loading
To provide immediate feedback while a remote or large resource is being fetched, you can set an initial image using `SetPlaceholderUrl`. For tiny assets where latency is not an issue, you can enable `SetSynchronousLoading`.

```cpp
imageView.SetPlaceholderUrl("images/loading.png")
         .SetSynchronousLoading(false); // Recommended for large/remote images
```

## N-Patch Support

`ImageView` automatically detects N-Patch images (e.g., `.9.png`) and applies the stretchable regions. You can also manually define borders for regular images to treat them as N-Patches.

```cpp
// Manually define N-Patch borders (left, top, right, bottom)
imageView.SetNPatchBorder(Vector4(12.0f, 12.0f, 12.0f, 12.0f))
         .SetNPatchBorderOnly(false);
```

## Monitoring Load Status

To synchronize UI changes with image availability, connect to the `ResourceReadySignal`. This signal fires when the image is fully loaded or if a failure occurs.

```cpp
// Connect to the signal
imageView.ResourceReadySignal().Connect(this, &MyApp::OnImageLoaded);

// Callback implementation
void OnImageLoaded(Dali::Ui::ImageView view) {
    if (view.GetLoadingStatus() == Dali::Ui::Visual::ResourceStatus::READY) {
        // The image is ready. Trigger an entry animation.
        view.Animate(Dali::Actor::Property::OPACITY, 1.0f, TimePeriod(0.5f));
    }
}
```
