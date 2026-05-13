---
title: Image
sidebar_label: Image
category: images-visuals
---

# Image

Use dali-ui image views as `Dali::Ui::View` objects, and keep image-specific behavior behind the public dali-ui image APIs.

## Table of Contents

- [Image Views in a dali-ui App](#image-views-in-a-dali-ui-app)
- [Tracking Image-Related Signals](#tracking-image-related-signals)
- [Dynamic Lottie Image Properties](#dynamic-lottie-image-properties)

## Image Views in a dali-ui App

`Dali::Ui::AnimatedImageView` is a dali-ui view type, so application code can keep image objects in the same `Dali::Ui::View` tree as the rest of the UI. Prefer storing or passing image widgets as `Dali::Ui::View` when a helper only needs to place, return, or compose the object.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

Dali::Ui::View CreateImageSlot(Dali::Ui::AnimatedImageView imageView)
{
  Dali::Ui::View view = imageView;
  return view;
}
```

Use `Dali::Ui::AnimatedImageView` when the image object itself must remain available for image-specific behavior, and use `Dali::Ui::View` at app composition boundaries.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

class ImagePanel
{
public:
  explicit ImagePanel(Dali::Ui::AnimatedImageView imageView)
  : mImageView(std::move(imageView))
  {
  }

  Dali::Ui::View GetView() const
  {
    return mImageView;
  }

private:
  Dali::Ui::AnimatedImageView mImageView;
};
```

## Tracking Image-Related Signals

When an image feature connects to public signals, use an owning controller derived from `Dali::ConnectionTracker`. This keeps signal connections tied to the controller lifetime and matches the pattern used by dali-ui image samples.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/animated-image-view.h>

class ImageController : public Dali::ConnectionTracker
{
public:
  ImageController()
  : Dali::ConnectionTracker()
  {
  }

  void SetImageView(Dali::Ui::AnimatedImageView imageView)
  {
    mImageView = std::move(imageView);
  }

private:
  Dali::Ui::AnimatedImageView mImageView;
};
```

Keep callbacks on the owning trait or class that exposes the signal. For image views that are also app-facing views, public resource and animation callbacks use `Dali::Ui::View` handles instead of raw actor objects.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/animated-image-view.h>

class ImageEventController : public Dali::ConnectionTracker
{
public:
  ImageEventController()
  : Dali::ConnectionTracker()
  {
  }

private:
  void OnImageEvent(Dali::Ui::View view)
  {
    mLastEventView = view;
  }

private:
  Dali::Ui::View mLastEventView;
};
```

## Dynamic Lottie Image Properties

For Lottie-backed image content, `Dali::Ui::LottieAnimation::DynamicPropertyInfo` describes a per-frame dynamic property binding for `Dali::Ui::LottieAnimationView::SetDynamicProperty()`. The structure is app-owned while being prepared, and its `callback` ownership is transferred after `SetDynamicProperty()` is called.

The fields are:

- `Dali::Ui::LottieAnimation::DynamicPropertyInfo::id`: an application-defined integer passed back to the callback.
- `Dali::Ui::LottieAnimation::DynamicPropertyInfo::keyPath`: the target layer or element path.
- `Dali::Ui::LottieAnimation::DynamicPropertyInfo::property`: the vector property selected for dynamic update.
- `Dali::Ui::LottieAnimation::DynamicPropertyInfo::callback`: the per-frame value callback.

```cpp
#include <dali-ui-foundation/public-api/image/lottie-animation-types.h>
#include <dali/public-api/signals/callback.h>

void PrepareDynamicImageProperty(Dali::Ui::LottieAnimation::DynamicPropertyInfo& info,
                                 Dali::CallbackBase* callback)
{
  info.id       = 1;
  info.keyPath  = "**";
  info.property = Dali::Ui::LottieAnimation::VectorProperty::FILL_COLOR;
  info.callback = callback;
}
```

Use a stable `id` when the same callback implementation serves several dynamic image updates. The callback can then branch on `Dali::Ui::LottieAnimation::DynamicPropertyInfo::id` while each `Dali::Ui::LottieAnimation::DynamicPropertyInfo` still owns a callback object for its own handoff.

```cpp
#include <dali-ui-foundation/public-api/image/lottie-animation-types.h>
#include <dali/public-api/signals/callback.h>

namespace
{
constexpr int32_t PRIMARY_IMAGE_TINT = 1;
constexpr int32_t ACCENT_IMAGE_TINT  = 2;
}

void PrepareDynamicImageTint(Dali::Ui::LottieAnimation::DynamicPropertyInfo& primary,
                             Dali::Ui::LottieAnimation::DynamicPropertyInfo& accent,
                             Dali::CallbackBase* primaryCallback,
                             Dali::CallbackBase* accentCallback)
{
  primary.id       = PRIMARY_IMAGE_TINT;
  primary.keyPath  = "**";
  primary.property = Dali::Ui::LottieAnimation::VectorProperty::FILL_COLOR;
  primary.callback = primaryCallback;

  accent.id       = ACCENT_IMAGE_TINT;
  accent.keyPath  = "**";
  accent.property = Dali::Ui::LottieAnimation::VectorProperty::FILL_COLOR;
  accent.callback = accentCallback;
}
```

Because `Dali::Ui::LottieAnimation::DynamicPropertyInfo::callback` is transferred to the receiving visual, allocate or create it exactly for that handoff and do not reuse the same callback pointer in another `Dali::Ui::LottieAnimation::DynamicPropertyInfo`. The callback is invoked on a worker thread, so it must return the frame value without calling DALi APIs.

```cpp
#include <dali-ui-foundation/public-api/image/lottie-animation-types.h>
#include <dali/public-api/signals/callback.h>

Dali::Ui::LottieAnimation::DynamicPropertyInfo MakeDynamicPropertyInfo(Dali::CallbackBase* callback)
{
  Dali::Ui::LottieAnimation::DynamicPropertyInfo info;
  info.id       = 10;
  info.keyPath  = "**";
  info.property = Dali::Ui::LottieAnimation::VectorProperty::FILL_COLOR;
  info.callback = callback;
  return info;
}
```
