# Image Prose Review

Changed prose:
- Clarified the overview to include loading behavior and layout alongside events and animation customization.
- Replaced the vague phrase “context symbol” for `Dali::Ui::AnimatedImageView` with concrete public API facts: it is a `Dali::Ui::View` subclass and exposes typed resource, playback, loading, masking, and signal APIs.
- Narrowed `Dali::Application` wording to setup and lifecycle ownership, and clarified that `Dali::Accessibility::Application` does not alter the image view model.
- Replaced generic `Dali::Ui::StackLayoutParams` wording with its documented role as per-child layout parameters for `Dali::Ui::StackLayout`.
- Corrected the Lottie section to say `DynamicPropertyInfo` targets Lottie animation content and is used by `SetDynamicProperty()`, not a generic “owning Lottie image API.”
- Corrected callback guidance: `callback` is invoked on a worker thread, ownership transfers after `SetDynamicProperty()` is called, and DALi APIs must not be called from it.
- Added concrete owning signal examples for event connections: `ResourceReadySignal()`, `AnimationFinishedSignal()`, and `InteractiveTrait::ClickedSignal()`.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/animated-image-view.h`: `AnimatedImageView : public View`; typed setters include `SetResourceUrl()`, `SetResourceUrls()`, `Play()`, `Pause()`, `Stop()`, `SetLoopCount()`, loading APIs, and `ResourceReadySignal()` / `AnimationFinishedSignal()`.
- `repos/dali-ui/dali-ui-foundation/public-api/image/lottie-animation-types.h`: `DynamicPropertyInfo` fields are `id`, `keyPath`, `property`, and `callback`; callback ownership transfers after `SetDynamicProperty()` and callback runs on a worker thread.
- `repos/dali-ui/dali-ui-foundation/public-api/lottie-animation-view.h`: `SetDynamicProperty(const LottieAnimation::DynamicPropertyInfo& info)` is the public Lottie view API.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/stack-layout-params.h`: `StackLayoutParams` stores per-child layout parameters for `StackLayout` and is attached with `View::SetLayoutParams()`.
- `repos/dali-core/dali/public-api/signals/connection-tracker.h`: `ConnectionTracker` is a concrete tracking base with `DisconnectAll()` and signal connection tracking.
- `repos/dali-ui/samples/image-view/animated-image-view-example.cpp`: sample connects `AnimationFinishedSignal()` and `ResourceReadySignal()` on `AnimatedImageView`, uses fluent view-tree construction, and uses `ConnectionTracker`.
- `repos/dali-ui/samples/image-view/image-loading-policy-example.cpp`: sample keeps image, animated image, and Lottie views in a `Dali::Ui::View` flow and connects UI button signals via `EnsureInteractiveTrait().ClickedSignal()`.

Remaining concerns:
- The code blocks were preserved as requested, but they remain intentionally skeletal and do not demonstrate full `AnimatedImageView::New(...).Set...` construction or real signal connection callbacks.
- The draft focuses on `AnimatedImageView` and Lottie dynamic properties; static `Dali::Ui::ImageView` exists in the repository but was not introduced because the supplied draft structure did not include a static image view section and section restructuring was disabled.
