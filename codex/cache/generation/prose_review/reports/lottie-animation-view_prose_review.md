# Lottie Animation View Prose Review

Changed prose:
- Tightened `AnimationFinishedSignal()` wording from "playback completes all configured loops" to "playback finishes all configured loops" to match the public header's signal description.
- Clarified `ResourceReadySignal()` as firing when the visual resource finishes loading or reports a later readiness event, matching `View::ResourceReadySignal()` and `NOTIFY_AFTER_RASTERIZATION`.
- Changed synchronous loading from "main thread" to "event thread", matching the public property/header wording.
- Changed release policy wording from "cached image resources" to "visual's texture resources", matching the public property description.
- Changed render-size prose and property table entries from fixed "size" wording to "rasterization size" and "hint" wording, matching `SetDesiredWidth()`, `SetDesiredHeight()`, and property docs.
- Clarified `SetNotifyAfterRasterization()` as notifying after each rasterization completes.
- Clarified `GetContentInfo()` and `GetMarkerInfo()` map values as two-element integer arrays `[startFrame, endFrame]`, matching the public header.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/lottie-animation-view.h`
- `repos/dali-ui/dali-ui-foundation/public-api/lottie-animation-view-properties.h`
- `repos/dali-ui/dali-ui-foundation/public-api/image/lottie-animation-types.h`
- `repos/dali-ui/dali-ui-foundation/integration-api/lottie-animation-view-impl.cpp`
- `repos/dali-ui/samples/image-view/lottie-animation-view-example.cpp`
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-LottieAnimationView.cpp`

Remaining concerns:
- The `CreateColorCallback()` helper in the dynamic-property snippet is app-local placeholder code, not a DALi API. The surrounding prose identifies the public API as `SetDynamicProperty()` and `DynamicPropertyInfo`, so the snippet was preserved under the localized-edit and preserve-code-block rules.
