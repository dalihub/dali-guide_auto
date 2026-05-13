---
title: Image
sidebar_label: Image
category: images-visuals
---

# Image

Use dali-ui image views for application-facing image content, and keep image-related event handling and animation customization in the `Dali::Ui::View` layer.

## Table of Contents

- [Image Views in dali-ui](#image-views-in-dali-ui)
- [Keeping Image Code in the View Layer](#keeping-image-code-in-the-view-layer)
- [Lottie Dynamic Image Properties](#lottie-dynamic-image-properties)
- [Connection Lifetime for Image Events](#connection-lifetime-for-image-events)

## Image Views in dali-ui

In a dali-ui app, image UI should be modeled as `Dali::Ui::View` content. `Dali::Ui::AnimatedImageView` is the app-facing image view type for animated image resources. Treat it as part of the view tree instead of dropping down to raw actor-style image handling.

`Dali::Ui::AnimatedImageView` is a context symbol in this guide: use it as the image view object you place in UI composition, while keeping concrete image behavior to the public image view APIs available in your installed dali-ui headers.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

namespace Ui = Dali::Ui;

class ImagePanel
{
public:
  void StoreImageView(Ui::AnimatedImageView imageView)
  {
    mImageView = imageView;
  }

  Ui::AnimatedImageView GetImageView() const
  {
    return mImageView;
  }

private:
  Ui::AnimatedImageView mImageView;
};
```

Because `Dali::Ui::AnimatedImageView` is a `Dali::Ui::View`-based type, pass it through APIs that accept view objects in your UI layer. This keeps image content aligned with the rest of the dali-ui application model.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

namespace Ui = Dali::Ui;

struct ImageSlot
{
  Ui::AnimatedImageView image;
};

void ReplaceImageSlot(ImageSlot& slot, Ui::AnimatedImageView nextImage)
{
  slot.image = nextImage;
}
```

## Keeping Image Code in the View Layer

Use `Dali::Application` only for application setup and lifecycle ownership. Image view code should remain app-facing and view-oriented. `Dali::Accessibility::Application` can be used as background application infrastructure when your app integrates accessibility, but image UI itself should still be represented through dali-ui views.

```cpp
#include <dali/public-api/adaptor-framework/application.h>
#include <dali-ui-foundation/public-api/animated-image-view.h>

namespace Ui = Dali::Ui;

class ImageApplicationState
{
public:
  explicit ImageApplicationState(Dali::Application application)
  : mApplication(std::move(application))
  {
  }

  void SetPrimaryImage(Ui::AnimatedImageView view)
  {
    mPrimaryImage = view;
  }

private:
  Dali::Application    mApplication;
  Ui::AnimatedImageView mPrimaryImage;
};
```

When layout-specific setup is needed, `Dali::Ui::StackLayoutParams` is a context symbol for view layout metadata. Keep that layout data separate from image behavior so that image ownership, event handling, and layout remain easy to reason about.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>
#include <dali-ui-foundation/public-api/layouts/stack-layout-params.h>

namespace Ui = Dali::Ui;

struct ImageLayoutBinding
{
  Ui::AnimatedImageView image;
  Ui::StackLayoutParams layout;
};

ImageLayoutBinding MakeImageLayoutBinding(
  Ui::AnimatedImageView image,
  Ui::StackLayoutParams layout)
{
  return ImageLayoutBinding{image, layout};
}
```

## Lottie Dynamic Image Properties

`Dali::Ui::LottieAnimation::DynamicPropertyInfo` describes a dynamic, per-frame property target for Lottie image animation content. It is owned content for image animation customization because its members define the target and callback binding used by the dynamic property mechanism.

Use `Dali::Ui::LottieAnimation::DynamicPropertyInfo::id` as the application-defined identifier. Use `Dali::Ui::LottieAnimation::DynamicPropertyInfo::keyPath` to address the Lottie layer or element path. Use `Dali::Ui::LottieAnimation::DynamicPropertyInfo::property` for the animated vector property selection, and `Dali::Ui::LottieAnimation::DynamicPropertyInfo::callback` for the per-frame value provider.

```cpp
#include <dali-ui-foundation/public-api/image/lottie-animation-types.h>

namespace Ui = Dali::Ui;

void SetDynamicTarget(
  Ui::LottieAnimation::DynamicPropertyInfo& info,
  int targetId,
  const char* keyPath)
{
  info.id      = targetId;
  info.keyPath = keyPath;
}
```

The `callback` member is invoked from the animation path, and ownership is transferred when the dynamic property is applied by the owning Lottie image API. Keep callback code independent from DALi object mutation; prepare values from data owned by your application model instead.

```cpp
#include <dali-ui-foundation/public-api/image/lottie-animation-types.h>

namespace Ui = Dali::Ui;

class LottieImageDynamicTarget
{
public:
  void ConfigureTarget(Ui::LottieAnimation::DynamicPropertyInfo& info)
  {
    info.id      = 1001;
    info.keyPath = "**";
  }

  int GetTargetId(const Ui::LottieAnimation::DynamicPropertyInfo& info) const
  {
    return info.id;
  }

  const Dali::String& GetTargetPath(const Ui::LottieAnimation::DynamicPropertyInfo& info) const
  {
    return info.keyPath;
  }
};
```

For reusable image animation code, store `DynamicPropertyInfo` near the feature that owns the visual effect. That keeps `id`, `keyPath`, `property`, and `callback` together and avoids splitting the target definition across unrelated UI code.

```cpp
#include <dali-ui-foundation/public-api/image/lottie-animation-types.h>

namespace Ui = Dali::Ui;

struct LottieImageEffect
{
  Ui::LottieAnimation::DynamicPropertyInfo dynamicProperty;
};

void AssignDynamicPropertyId(LottieImageEffect& effect, int id)
{
  effect.dynamicProperty.id = id;
}
```

## Connection Lifetime for Image Events

Image views commonly participate in user or resource events. Use `Dali::ConnectionTracker` as the owning base for objects that connect to public signals, so connections are tied to the lifetime of the controller object.

`Dali::ConnectionTracker::ConnectionTracker` constructs the tracking base. Derive your image controller from `Dali::ConnectionTracker`, then connect signals from their owning view or trait APIs.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/animated-image-view.h>

namespace Ui = Dali::Ui;

class ImageEventController : public Dali::ConnectionTracker
{
public:
  ImageEventController()
  : Dali::ConnectionTracker()
  {
  }

  void SetImageView(Ui::AnimatedImageView imageView)
  {
    mImageView = imageView;
  }

private:
  Ui::AnimatedImageView mImageView;
};
```

Keep signal connections scoped to the object that owns the image behavior. This is especially useful when an image view is rebuilt, replaced, or removed as part of a dali-ui view tree update.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/animated-image-view.h>

namespace Ui = Dali::Ui;

class ImageScreenController : public Dali::ConnectionTracker
{
public:
  ImageScreenController()
  : Dali::ConnectionTracker()
  {
  }

  void BindImage(Ui::AnimatedImageView image)
  {
    mImage = image;
  }

  Ui::AnimatedImageView GetBoundImage() const
  {
    return mImage;
  }

private:
  Ui::AnimatedImageView mImage;
};
```
