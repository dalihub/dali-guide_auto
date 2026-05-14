---
title: Image
sidebar_label: Image
category: images-visuals
---

# Image

Use dali-ui image views as `Dali::Ui::View` objects in your app view tree, and keep image-related signal connections tracked by `Dali::ConnectionTracker`.

## Table of Contents

- [Use Image Views as dali-ui Views](#use-image-views-as-dali-ui-views)
- [Own Image Event Connections](#own-image-event-connections)
- [Keep Animated Image Handles Movable](#keep-animated-image-handles-movable)
- [Configure Lottie Dynamic Property Metadata](#configure-lottie-dynamic-property-metadata)
- [Reuse Stack Layout Parameters Around Image Views](#reuse-stack-layout-parameters-around-image-views)

## Use Image Views as dali-ui Views

In a dali-ui application, image UI should be treated as part of the `Dali::Ui::View` hierarchy. For animated image content, keep the app-facing handle as `Dali::Ui::AnimatedImageView` and pass it through your own UI-building code as a view object rather than passing it around as `Dali::Actor`.

```cpp
#include <utility>

#include <dali-ui-foundation/public-api/animated-image-view.h>

Dali::Ui::AnimatedImageView KeepImageHandle(Dali::Ui::AnimatedImageView imageView)
{
  return Dali::Ui::AnimatedImageView(std::move(imageView));
}
```

`Dali::Ui::AnimatedImageView` is a dali-ui view type for animated image resources such as GIF files. Keep it in the same ownership style as the rest of your dali-ui screen: create or receive the handle in your controller, attach it to the surrounding view tree, and keep layout decisions in dali-ui layout objects.

## Own Image Event Connections

Image screens commonly react to loading, playback, or input events. Store those connections in a controller that inherits `Dali::ConnectionTracker`; this matches the public signal connection style used by dali-ui samples and keeps signal lifetime bound to the controller.

```cpp
#include <dali/public-api/signals/connection-tracker.h>

class ImageScreenController : public Dali::ConnectionTracker
{
public:
  ImageScreenController()
  : Dali::ConnectionTracker()
  {
  }
};
```

Use `Dali::ConnectionTracker` as the tracker for callbacks that belong to the image screen. This is especially useful when an image view is rebuilt, hidden, or replaced while the controller remains responsible for the screen state.

## Keep Animated Image Handles Movable

`Dali::Ui::AnimatedImageView` supports move construction, so controller code can transfer a handle without requiring an extra handle copy. This is useful when your image-building helper returns the configured animated image view to a parent screen builder.

```cpp
#include <utility>

#include <dali-ui-foundation/public-api/animated-image-view.h>

class AnimatedImageSlot
{
public:
  explicit AnimatedImageSlot(Dali::Ui::AnimatedImageView imageView)
  : mImageView(std::move(imageView))
  {
  }

private:
  Dali::Ui::AnimatedImageView mImageView;
};
```

The moved `Dali::Ui::AnimatedImageView` remains the app-facing image view handle. Keep subsequent image UI work on the moved-to handle.

## Configure Lottie Dynamic Property Metadata

Lottie animation views can use `Dali::Ui::LottieAnimation::DynamicPropertyInfo` to describe dynamic property metadata. The struct owns the Lottie targeting fields: `Dali::Ui::LottieAnimation::DynamicPropertyInfo::id`, `Dali::Ui::LottieAnimation::DynamicPropertyInfo::keyPath`, `Dali::Ui::LottieAnimation::DynamicPropertyInfo::property`, and `Dali::Ui::LottieAnimation::DynamicPropertyInfo::callback`.

```cpp
#include <dali-ui-foundation/public-api/image/lottie-animation-types.h>

void SetLottieTarget(Dali::Ui::LottieAnimation::DynamicPropertyInfo& info)
{
  info.id      = 7;
  info.keyPath = "**";
}
```

Use `Dali::Ui::LottieAnimation::DynamicPropertyInfo::id` as the stable identifier passed back to the dynamic-property callback. Use `Dali::Ui::LottieAnimation::DynamicPropertyInfo::keyPath` to target the Lottie layer or element path; `"**"` targets all matching content.

`Dali::Ui::LottieAnimation::DynamicPropertyInfo::property` identifies the vector property to animate, and `Dali::Ui::LottieAnimation::DynamicPropertyInfo::callback` supplies the per-frame value callback. Ownership of the callback is transferred when `SetDynamicProperty()` is called, and the callback runs on a worker thread, so do not call DALi APIs from that callback.

## Reuse Stack Layout Parameters Around Image Views

When an image view is placed in a stack layout, `Dali::Ui::StackLayoutParams` can be copied into a new params handle for nearby image views. This keeps image sizing policy separate from the image handle itself.

```cpp
#include <dali-ui-foundation/public-api/layouts/stack-layout-params.h>

Dali::Ui::StackLayoutParams DuplicateImageLayoutParams(
  const Dali::Ui::StackLayoutParams& sourceParams)
{
  return Dali::Ui::StackLayoutParams::New(sourceParams);
}
```

Use `Dali::Ui::StackLayoutParams` for per-child layout data around image content. The image view remains the visible `Dali::Ui::View`; the layout params describe how that view participates in its stack container.
