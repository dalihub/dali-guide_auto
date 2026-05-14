# Image Prose Review

Changed prose:
- Replaced “signal ownership tied to `Dali::ConnectionTracker`” with “signal connections tracked by `Dali::ConnectionTracker`” to match the tracker’s role.
- Replaced “dropping down to a raw actor model” with “passing it around as `Dali::Actor`” for a more precise app-facing contrast.
- Clarified `Dali::Ui::AnimatedImageView` as a view for animated resources “such as GIF files,” matching the public header.
- Broadened event wording from “resource or playback events” to “loading, playback, or input events,” matching `ResourceReadySignal()`, `AnimationFinishedSignal()`, timer/key/input usage in samples.
- Corrected movable-handle prose so it does not imply a DALi handle copy duplicates the underlying view object.
- Changed “Lottie images” to “Lottie animation views” and “image-side targeting fields” to “Lottie targeting fields.”
- Clarified `callback` ownership transfer happens when `SetDynamicProperty()` is called.
- Corrected the `StackLayoutParams` copy example to use `Dali::Ui::StackLayoutParams::New(sourceParams)` instead of the copy constructor, because the public API documents `New(const StackLayoutParams&)` as creating a new params handle with copied values.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/animated-image-view.h`: `Dali::Ui::AnimatedImageView` derives from `View`, supports move construction, and exposes `ResourceReadySignal()` and `AnimationFinishedSignal()`.
- `repos/dali-ui/samples/image-view/animated-image-view-example.cpp`: sample controller inherits `ConnectionTracker`, connects timer, application, window, resource-ready, and animation-finished signals, and builds image UI with `StackLayout` and `AnimatedImageView`.
- `repos/dali-core/dali/public-api/signals/connection-tracker.h`: `ConnectionTracker` tracks signal connections and provides `DisconnectAll()`.
- `repos/dali-ui/dali-ui-foundation/public-api/image/lottie-animation-types.h`: `DynamicPropertyInfo` fields are `id`, `keyPath`, `property`, and `callback`; callback ownership is transferred after `SetDynamicProperty()` and callback runs on a worker thread.
- `repos/dali-ui/dali-ui-foundation/public-api/lottie-animation-view.h`: `SetDynamicProperty()` takes `LottieAnimation::DynamicPropertyInfo`.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/stack-layout-params.h` and `.cpp`: `StackLayoutParams::New(const StackLayoutParams&)` creates a new params handle with copied values; `View::SetLayoutParams()` attaches params to a child view.

Remaining concerns:
- The draft remains intentionally narrow and does not cover richer image APIs such as `Dali::Ui::ImageView`, fitting mode, pixel area, placeholders, alpha masks, loading policy, or animated playback controls, even though the samples show those as practical app-facing topics.
