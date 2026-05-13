# Image Prose Review

Changed prose:
- Clarified the signal controller wording from a hard requirement to a usage pattern: "use an owning controller derived from `Dali::ConnectionTracker`."
- Corrected signal callback prose to say public resource and animation callbacks use `Dali::Ui::View` handles.
- Changed `ConnectionTracker` includes from `<dali/public-api/object/connection-tracker.h>` to `<dali/public-api/signals/connection-tracker.h>`.
- Grounded Lottie dynamic property prose in `Dali::Ui::LottieAnimationView::SetDynamicProperty()`.
- Added the worker-thread restriction for dynamic property callbacks.
- Changed the shared-callback guidance so it does not imply one transferred callback pointer can be reused.
- Added `info.property = Dali::Ui::LottieAnimation::VectorProperty::FILL_COLOR;` to each dynamic-property example so `DynamicPropertyInfo::property` is initialized.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/animated-image-view.h` defines `Dali::Ui::AnimatedImageView : public View`, `AnimationFinishedSignalType` as `Signal<void(View)>`, and `ResourceReadySignal()`.
- `repos/dali-core/dali/public-api/signals/connection-tracker.h` defines `Dali::ConnectionTracker`; the public include path is under `signals`.
- `repos/dali-ui/samples/image-view/animated-image-view-example.cpp` uses a controller derived from `ConnectionTracker` and connects `AnimationFinishedSignal()` and `ResourceReadySignal()`.
- `repos/dali-ui/dali-ui-foundation/public-api/lottie-animation-view.h` exposes `SetDynamicProperty(const LottieAnimation::DynamicPropertyInfo& info)` and states callback ownership is transferred to the visual.
- `repos/dali-ui/dali-ui-foundation/public-api/image/lottie-animation-types.h` defines `DynamicPropertyInfo` fields and notes that the callback is invoked on a worker thread and must not call DALi APIs.
- `repos/dali-ui/dali-ui-foundation/public-api/image/lottie-animation-enumerations.h` defines `Dali::Ui::LottieAnimation::VectorProperty`, including `FILL_COLOR`.

Remaining concerns:
- The dynamic Lottie examples still show preparation helpers rather than a full `LottieAnimationView::SetDynamicProperty()` call, preserving the draft's code-block structure as requested.
