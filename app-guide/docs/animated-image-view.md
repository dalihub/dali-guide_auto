---
id: animated-image-view
title: "animated-image-view"
sidebar_label: "animated-image-view"
---
## Overview

AnimatedImageView is a specialized UI component designed for [rendering](./rendering.md) multi-frame GIF and WEBP animations within DALi applications. It provides a robust set of controls to manage [animation](./animation.md) timing, playback cycles, and resource lifecycle management for complex visual content.

## Getting Started

To utilize animated content, instantiate the component using the factory method. Once created, the source file can be assigned to begin the process of loading the image resource.

```cpp
AnimatedImageView animatedImageView = AnimatedImageView::New("image.gif");
```

The component provides a static creation method that accepts an optional URL. Initializing the object via the factory method ensures that the underlying view is correctly prepared for animation playback.

## Playback Control

Animation playback can be manipulated dynamically throughout the lifecycle of the component. The API allows for standard state transitions including starting, pausing, and stopping the animation.

```cpp
AnimatedImageView animatedImageView = AnimatedImageView::New("image.gif");
animatedImageView.SetLoopCount(3);
animatedImageView.Play();
animatedImageView.Pause();
animatedImageView.Stop();
```

The playback state is managed through distinct methods. Calling the playback start method resumes the sequence from the current frame. The stop method halts the sequence and resets the internal frame counter to the beginning. The loop count can be defined to control how many times the animation cycles before concluding. If a specific frame needs to be accessed, jumping to that index can be performed manually.

## Loading and Resource Management

Memory performance can be optimized by adjusting how the framework handles image assets. This includes controlling the batching of frames and the total number of frames kept within the active cache.

```cpp
AnimatedImageView animatedImageView = AnimatedImageView::New("image.gif");
animatedImageView.SetLoadPolicy(LoadPolicy::IMMEDIATE);
animatedImageView.SetBatchSize(5);
animatedImageView.SetCacheSize(10);
```

The loading policy determines if resources are retrieved immediately upon object creation or lazily when the view is attached to the scene. Setting the release policy allows developers to specify whether assets should be discarded when the view is detached or destroyed. Synchronous loading can be enabled if the application requires the image to be fully ready before proceeding with other operations.

## Visual Configuration and Masking

Visual properties such as tinting and spatial fitting can be applied to ensure the animation integrates seamlessly with the application UI. The component also supports alpha masking to create complex, non-rectangular visual effects.

```cpp
AnimatedImageView animatedImageView = AnimatedImageView::New("image.gif");
animatedImageView.SetImageColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f));
animatedImageView.SetFittingMode(FittingMode::FIT_KEEP_ASPECT_RATIO);
animatedImageView.SetAlphaMaskUrl("mask.png");
animatedImageView.SetMaskingMode(MaskingType::MASKING_ON_RENDERING);
```

The fitting mode property dictates how the animation scales relative to its container. Masking can be applied during either the loading or rendering phase, and enabling crop-to-mask ensures that content outside the mask boundaries is excluded. A placeholder URL can also be assigned to provide a static fallback image while the primary animated resource is in the process of being loaded.

## Layout and Sizing

Size constraints act as hints to the image loading system, ensuring the underlying buffers are scaled efficiently for the target view dimensions. By providing specific width and height dimensions, the framework can optimize memory allocation for high-resolution animations.

```cpp
AnimatedImageView animatedImageView = AnimatedImageView::New("image.gif");
animatedImageView.SetDesiredWidth(200);
animatedImageView.SetDesiredHeight(200);
animatedImageView.SetImageLoadWithViewSize(true);
```

The view size can be enforced during the load process to match the final display bounds. This reduces memory overhead by ensuring the decoded frames correspond exactly to the required display area.

## Event Handling

Signals are provided to enable reactive programming, allowing the application to respond to changes in the animation state or resource availability. These signals should be connected using standard signal handler patterns.

```cpp
class MyHandler
{
public:
  void OnResourceReady(AnimatedImageView view) {}
  void OnAnimationFinished(AnimatedImageView view) {}
};

// ... inside a function ...
AnimatedImageView animatedImageView = AnimatedImageView::New("image.gif");
MyHandler* myHandler = new MyHandler();
animatedImageView.ResourceReadySignal().Connect(myHandler, &MyHandler::OnResourceReady);
animatedImageView.AnimationFinishedSignal().Connect(myHandler, &MyHandler::OnAnimationFinished);
```

The resource ready signal is triggered when the image is fully prepared for display. The [animation](./animation.md) finished signal is emitted once all defined loops have completed, allowing the application to trigger follow-up animations or clean up secondary logic.

## Related Sub-Components

- [animated-image-view-types](./animated-image-view-types.md): Defines the enumerations and types used for configuration, such as fitting modes and policies. → See: [[animated-image-view-types](./animated-image-view-types.md)]
- [animated-image-view-properties](./animated-image-view-properties.md): Lists the property indices for dynamic property access. → See: [[animated-image-view-properties](./animated-image-view-properties.md)]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/animated-image-view)
