---
title: Image
sidebar_label: Image
category: images-visuals
---

# Image

`dali-ui` image UI is built as `Dali::Ui::View` objects that can be placed in the view tree, configured with typed setters, and observed through signals.

## Table of Contents

- [Use Image Views in a dali-ui View Tree](#use-image-views-in-a-dali-ui-view-tree)
- [Play Animated Image Resources](#play-animated-image-resources)
- [React to Image Resource and Playback Events](#react-to-image-resource-and-playback-events)
- [Control Animated Image Loading and Frame Behavior](#control-animated-image-loading-and-frame-behavior)
- [Describe Dynamic Lottie Image Properties](#describe-dynamic-lottie-image-properties)

## Use Image Views in a dali-ui View Tree

In a `dali-ui` app, treat image widgets as `Dali::Ui::View` objects. This keeps image content compatible with layout containers, child insertion, and app-level view composition.

`Dali::Ui::AnimatedImageView` is a `Dali::Ui::View` for animated image resources such as GIF or animated WebP files. Create it with `Dali::Ui::AnimatedImageView::New`, then configure it through typed setters.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>
#include <dali-ui-foundation/public-api/layouts/stack-layout-params.h>

using namespace Dali;
using namespace Dali::Ui;

View CreateAnimatedImagePreview()
{
  AnimatedImageView image =
    AnimatedImageView::New("images/loading.gif")
      .SetLoopCount(-1)
      .SetFrameSpeedFactor(1.0f)
      .SetImageLoadWithViewSize(true)
      .SetLayoutParams(StackLayoutParams::New().SetWeight(1.0f));

  return image;
}
```

Use `Dali::Ui::StackLayoutParams` when the image participates in a stack layout. `StackLayoutParams::New().SetWeight(...)` gives the child a proportional share of the remaining space on the stack axis.

```cpp
StackLayoutParams params = StackLayoutParams::New().SetWeight(2.0f);
float weight = params.GetWeight();

AnimatedImageView image = AnimatedImageView::New("images/banner.webp");
image.SetLayoutParams(params);
```

## Play Animated Image Resources

`Dali::Ui::AnimatedImageView` exposes playback control through typed methods. Use `Play`, `Pause`, `Stop`, and `JumpToFrame` instead of manipulating lower-level actor properties.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

using namespace Dali;
using namespace Dali::Ui;

class GalleryPreview
{
public:
  GalleryPreview()
  : mImage(AnimatedImageView::New("images/hero-animation.webp")
             .SetLoopCount(3)
             .SetFrameDelay(80)
             .SetFrameSpeedFactor(1.0f))
  {
  }

  View GetView()
  {
    return mImage;
  }

  void Start()
  {
    mImage.Play();
  }

  void Pause()
  {
    mImage.Pause();
  }

  void StopAtBeginning()
  {
    mImage.Stop();
    mImage.JumpToFrame(0);
  }

private:
  AnimatedImageView mImage;
};
```

`SetLoopCount(-1)` configures infinite looping. `SetLoopCount(0)` prevents playback. A positive value plays that exact number of loops.

```cpp
AnimatedImageView spinner =
  AnimatedImageView::New("images/spinner.gif")
    .SetLoopCount(-1)
    .SetFrameSpeedFactor(1.5f);

spinner.Play();

int loopCount = spinner.GetLoopCount();
float speed = spinner.GetFrameSpeedFactor();
```

## React to Image Resource and Playback Events

Use `Dali::ConnectionTracker` as the owner for signal connections. This is the same pattern used by DALi samples: the controller derives from `ConnectionTracker`, then connects view signals to member callbacks.

`Dali::Ui::AnimatedImageView::ResourceReadySignal` is emitted when the image resource is ready to display. `Dali::Ui::AnimatedImageView::AnimationFinishedSignal` uses the signal type `Signal<void(Dali::Ui::View)>` and is emitted when the animation completes all configured loops.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/animated-image-view.h>

using namespace Dali;
using namespace Dali::Ui;

class ImageController : public ConnectionTracker
{
public:
  ImageController()
  : mImage(AnimatedImageView::New("images/done.gif").SetLoopCount(1))
  {
    mImage.ResourceReadySignal().Connect(this, &ImageController::OnResourceReady);
    mImage.AnimationFinishedSignal().Connect(this, &ImageController::OnAnimationFinished);
  }

  View GetView()
  {
    return mImage;
  }

  void PlayWhenVisible()
  {
    mImage.Play();
  }

private:
  void OnResourceReady(View view)
  {
    AnimatedImageView image = AnimatedImageView::DownCast(view);
    if(image)
    {
      image.Play();
    }
  }

  void OnAnimationFinished(View view)
  {
    AnimatedImageView image = AnimatedImageView::DownCast(view);
    if(image)
    {
      image.Stop();
    }
  }

private:
  AnimatedImageView mImage;
};
```

When a callback receives a `Dali::Ui::View`, downcast with `Dali::Ui::AnimatedImageView::DownCast` before calling animated-image-specific methods.

## Control Animated Image Loading and Frame Behavior

`Dali::Ui::AnimatedImageView` can be configured for loading hints, frame cache behavior, and display behavior with typed setters. These settings are useful for image-heavy views where decoding cost and playback behavior matter.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

using namespace Dali;
using namespace Dali::Ui;

AnimatedImageView CreateTimelinePreview()
{
  AnimatedImageView image =
    AnimatedImageView::New("images/story.gif")
      .SetDesiredWidth(320)
      .SetDesiredHeight(180)
      .SetBatchSize(2)
      .SetCacheSize(4)
      .SetFrameDelay(100)
      .SetLoopCount(1);

  image.SetImageLoadWithViewSize(true);
  return image;
}
```

Use frame query methods when updating app UI around playback state.

```cpp
void LogAnimatedImageState(AnimatedImageView image)
{
  Ui::AnimatedImage::PlayState state = image.GetPlayState();
  int currentFrame = image.GetCurrentFrame();
  int totalFrames = image.GetTotalFrame();
  int frameDelay = image.GetFrameDelay();

  (void)state;
  (void)currentFrame;
  (void)totalFrames;
  (void)frameDelay;
}
```

For stop behavior, use `Dali::Ui::AnimatedImage::StopBehavior` values with `SetStopBehavior`.

```cpp
AnimatedImageView image =
  AnimatedImageView::New("images/progress.gif")
    .SetLoopCount(1)
    .SetStopBehavior(Ui::AnimatedImage::StopBehavior::LAST_FRAME);

image.Play();

Ui::AnimatedImage::StopBehavior behavior = image.GetStopBehavior();
(void)behavior;
```

## Describe Dynamic Lottie Image Properties

`Dali::Ui::LottieAnimation::DynamicPropertyInfo` describes a dynamic property update for Lottie image content. It is owned API content for the image feature because its fields define how an app identifies the target element, property, callback, and application-defined identifier.

The structure has four public members:

- `Dali::Ui::LottieAnimation::DynamicPropertyInfo::id`: an application-defined integer passed back to the callback.
- `Dali::Ui::LottieAnimation::DynamicPropertyInfo::keyPath`: the Lottie target path, such as a specific layer path or `"**"` for all matching elements.
- `Dali::Ui::LottieAnimation::DynamicPropertyInfo::property`: the vector property to animate.
- `Dali::Ui::LottieAnimation::DynamicPropertyInfo::callback`: the callback object. Ownership is transferred to the visual after the dynamic property is applied.

The callback is invoked on a worker thread, so it must not call DALi APIs.

```cpp
#include <dali-ui-foundation/public-api/image/lottie-animation-types.h>

using namespace Dali;
using namespace Dali::Ui;

LottieAnimation::DynamicPropertyInfo CreateFillColorProperty(CallbackBase* callback)
{
  LottieAnimation::DynamicPropertyInfo info;
  info.id = 1;
  info.keyPath = "**";
  info.property = LottieAnimation::VectorProperty::FILL_COLOR;
  info.callback = callback;

  return info;
}
```

Keep `DynamicPropertyInfo` setup small and explicit. The `id` lets one callback distinguish multiple dynamic properties without requiring separate callback functions for every target.

```cpp
void ConfigureDynamicProperty(LottieAnimation::DynamicPropertyInfo& info,
                              int32_t id,
                              const Dali::String& keyPath,
                              CallbackBase* callback)
{
  info.id = id;
  info.keyPath = keyPath;
  info.property = LottieAnimation::VectorProperty::FILL_COLOR;
  info.callback = callback;
}
```
