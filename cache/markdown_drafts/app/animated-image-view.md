## Overview

AnimatedImageView is a specialized Dali::Ui::View component designed to efficiently load, display, and manage playback for animated GIF and WEBP image formats.`AnimatedImageView` It inherits from the core UI View structure, providing a high-level API for handling frame-based sequences while maintaining integration with the broader DALi UI layout system.

## Getting Started and Loading Resources

Developers can initialize the component by providing a source URL for the animation asset.`New(const Dali::String &)` The view supports both single-file animation sources and frame-by-frame arrays for custom sequences.`SetResourceUrl(const Dali::String &)` When using multiple source files, individual image paths are passed as a collection.`SetResourceUrls(const Dali::Vector< Dali::String > &)`

```cpp
AnimatedImageView animatedView = AnimatedImageView::New().SetResourceUrl("animation.gif");
```

Loading behavior can be customized to trigger immediately upon creation or defer until the view is attached to the scene.`Image::LoadPolicy::IMMEDIATE` Additionally, a placeholder image may be specified to provide immediate visual feedback while the primary animated resource is being fetched.`SetPlaceholderUrl(const Dali::String &)`

## Playback Control

The component provides comprehensive controls to manipulate the state of an active animation.`Play()` Users can transition between operational states, including resuming, pausing, and resetting the animation.`Pause()` When an animation is stopped, the final display state of the view can be configured to hold the current, first, or last frame.`SetStopBehavior(AnimatedImage::StopBehavior)`

```cpp
AnimatedImageView animatedView = AnimatedImageView::New();
animatedView.Play().SetStopBehavior(AnimatedImage::StopBehavior::FIRST_FRAME);
```

The playback lifecycle can be further tuned by adjusting the loop count and modifying the playback speed relative to the native frame delay.`SetLoopCount(int)` For precise temporal positioning, the view allows jumping to a specific index within the animation sequence.`JumpToFrame(int)`

## Visual Configuration and Masking

Visual presentation of the animated content is handled through various rendering properties.`SetImageColor(const UiColor &)` Users can tint the animation, adjust scaling parameters to ensure the image fits within the bounds, and toggle pre-multiplied alpha settings for correct blending.`SetFittingMode(Image::FittingMode)`

Complex UI requirements are supported through alpha masking, which allows the animated content to be clipped to a specific shape defined by an external mask image.`SetAlphaMaskUrl(const Dali::String &)` The engine also supports localized rendering by defining a specific pixel area to be displayed from the source images.`SetPixelArea(const Vector4 &)`

## Layout and Sizing

The component respects the standard layout policies inherited from the underlying UI View class.`SetDesiredWidth(int)` To optimize memory usage during decoding, developers can provide width and height hints that instruct the loader to scale the assets appropriately.`SetDesiredHeight(int)`

```cpp
AnimatedImageView animatedView = AnimatedImageView::New();
animatedView.SetImageLoadWithViewSize(true).SetDesiredWidth(200).SetDesiredHeight(200);
```

Loading efficiency is further improved by enabling a mode where the image is processed synchronously at the size of the container.`SetImageLoadWithViewSize(bool)` This ensures that the rendered output aligns with the allocated UI space, potentially reducing memory overhead in high-resolution scenarios.`IsImageLoadWithViewSizeEnabled()`

## Memory and Performance Optimization

Performance management is achieved through cache and batch settings that control how frames are buffered during playback.`SetCacheSize(int)` By adjusting the batch size, applications can balance memory consumption against the smoothness of frame transitions.`SetBatchSize(int)`

Resource cleanup is managed through policies that dictate when the underlying image data should be released from the cache.`SetReleasePolicy(Image::ReleasePolicy)` Setting these policies correctly is crucial for long-running applications that cycle through multiple animated assets.`Image::ReleasePolicy::DESTROYED`

## Event Handling

The component provides signals to facilitate reactivity to the animation and resource lifecycle.`ResourceReadySignal()` When the image asset is successfully loaded and processed, the associated signal is emitted to notify the application that the view is ready to render.`ResourceReadySignalType`

Playback milestones, specifically the completion of all defined loops, are tracked via a dedicated signal.`AnimationFinishedSignal()` This allows developers to trigger dependent UI actions, such as chaining animations or hiding the view, exactly when the playback finishes.`AnimationFinishedSignalType`

```cpp
class MyHandler
{
public:
  void OnResourceReady(AnimatedImageView view) {}
  void OnAnimationFinished(AnimatedImageView view) {}
};

void SetupSignals(AnimatedImageView view, MyHandler* handler)
{
  view.ResourceReadySignal().Connect(handler, &MyHandler::OnResourceReady);
  view.AnimationFinishedSignal().Connect(handler, &MyHandler::OnAnimationFinished);
}
```

## Related Sub-Components

*   Properties defining the behavioral and visual attributes of the animated view. → See: [animated-image-view-properties]