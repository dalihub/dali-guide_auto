---
id: animated-image-view
title: "animated-image-view"
sidebar_label: "animated-image-view"
---
# Overview

`AnimatedImageView` is a specialized `View` component designed to efficiently load, display, and manage playback for animated GIF and WEBP image resources within a DALi interface. This component provides high-level control over frame-by-frame [rendering](./rendering.md) and lifecycle management for animated assets.

# Initialization and Resource Loading

The `AnimatedImageView` component supports both single-file animated formats and arrays of individual image frames. Developers can specify the resource location during initialization to prepare the view for [rendering](./rendering.md).

```cpp
AnimatedImageView animatedImageView = AnimatedImageView::New("image_url.gif");
```

The view supports the use of placeholder images to maintain a consistent UI state while the primary animated resource is being fetched. Setting a loading policy ensures that resources are requested only when required by the application lifecycle, such as when the component is attached to the screen or during initial creation.

```cpp
AnimatedImageView animatedImageView = AnimatedImageView::New();
animatedImageView.SetPlaceholderUrl("placeholder.png");
animatedImageView.SetLoadPolicy(LoadPolicy::IMMEDIATE);
```

# Playback Control and Animation State

The playback lifecycle is managed through explicit command methods, allowing developers to start, pause, or stop the animation at any time. The component also enables precise frame control, including the ability to jump to a specific index or adjust the speed of the animation.

```cpp
AnimatedImageView animatedImageView = AnimatedImageView::New();
animatedImageView.Play();
animatedImageView.Pause();
animatedImageView.JumpToFrame(5);
```

Looping behavior can be configured to repeat the animation a specific number of times, and the stop behavior can be customized to handle the final state of the image once the playback completes.



# Visual Configuration and Effects

The visual appearance of the animated content can be adjusted to match the application's design language, including color tinting and alpha transparency handling. Advanced visual effects such as alpha masking can be applied to provide complex clipping shapes for the animated frames.

```cpp
AnimatedImageView animatedImageView = AnimatedImageView::New();
animatedImageView.SetImageColor(UiColor(1.0f, 0.5f, 0.0f, 1.0f));
animatedImageView.SetAlphaMaskUrl("mask.png");
```

The component provides various fitting modes to determine how the image content scales within the defined boundaries of the view, ensuring visual consistency across different aspect ratios.

# Layout and Sizing Preferences

Managing the memory footprint is simplified by providing target dimension hints to the image loader. By specifying desired width and height, the component can optimize the decoding process to match the actual display size, reducing unnecessary memory allocation.

```cpp
AnimatedImageView animatedImageView = AnimatedImageView::New();
animatedImageView.SetDesiredWidth(200);
animatedImageView.SetDesiredHeight(200);
```

In addition to base sizing, the component supports pixel area configuration, which allows for rendering only a specific sub-region of the source images.

# Performance and Memory Management

For complex animations, adjusting the batch and cache sizes provides developers with the ability to tune the balance between memory consumption and playback smoothness. Batching ensures that frames are loaded in logical chunks, while the cache maintains a buffer of decoded frames ready for immediate display.

```cpp
AnimatedImageView animatedImageView = AnimatedImageView::New();
animatedImageView.SetBatchSize(10);
animatedImageView.SetCacheSize(50);
```

The release policy is a critical configuration for memory management, determining whether cached resources are purged when the view is detached from the scene or destroyed.

# Event Handling and Signaling

The component exposes signals to notify the application when the animated content is fully prepared for playback or when a sequence of animation loops has completed. These signals are essential for synchronizing the UI with the readiness of the underlying assets.

```cpp
AnimatedImageView animatedImageView = AnimatedImageView::New();

animatedImageView.ResourceReadySignal().Connect(this, &MyHandler::OnResourceReady);
animatedImageView.AnimationFinishedSignal().Connect(this, &MyHandler::OnAnimationFinished);
```

---

### Related Sub-Components

- `[animated-image-view-types](./animated-image-view-types.md)`: Defines the enumerations and supporting types used for controlling animation states and behavior. → See: [animated-image-view-types]
- `[animated-image-view-properties](./animated-image-view-properties.md)`: Provides a comprehensive mapping of property indices for programmatic access to view attributes. → See: [[animated-image-view-properties](./animated-image-view-properties.md)]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/animated-image-view)
