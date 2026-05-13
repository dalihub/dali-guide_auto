# Lottie Animation View Prose Review

Changed prose:
- Clarified that `SetDesiredWidth` and `SetDesiredHeight` are rasterization size hints, not general layout sizing APIs.
- Added the returned map shape for `GetContentInfo` and `GetMarkerInfo`: names map to two-element integer frame ranges.
- Changed synchronous loading wording from "main thread" to "event thread" to match public API wording.
- Clarified `SetRenderScale` as relative to visual size.
- Reworded `SetNotifyAfterRasterization` to avoid overstating signal behavior and match the public option name.
- Clarified `SetPixelArea` as a normalized sub-region of the animation texture.
- Added that `SetDynamicProperty` should be called after a resource URL is set so a visual can be created.
- Adjusted color examples to use valid `UiColor` construction for RGB/float RGBA values.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/lottie-animation-view.h`: confirms `LottieAnimationView` inherits `View`, `New`, `SetResourceUrl`, playback methods, frame and marker range APIs, signals, typed setters/getters, and dynamic property callback ownership/thread note.
- `repos/dali-ui/dali-ui-foundation/public-api/lottie-animation-view-properties.h`: confirms property names, types, defaults, desired size hint wording, `PIXEL_AREA` normalized coordinates, `RENDER_SCALE`, and notify-after-rasterization property.
- `repos/dali-ui/dali-ui-foundation/integration-api/lottie-animation-view-impl.cpp`: confirms typed setters update visual state, `GetContentInfo`/`GetMarkerInfo` return maps from visual properties, `SetDynamicProperty` is ignored if no visual exists, placeholder behavior, and signal emission with `Dali::Ui::View`.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-LottieAnimationView.cpp`: confirms defaults and round-trip behavior for loop count, stop behavior, looping mode, speed factor, image color, desired size, release policy, synchronous loading, pre-multiplied alpha, redraw flags, frame cache, notify-after-rasterization, render scale, and empty content/marker maps without a visual.
- `repos/dali-ui/samples/image-view/lottie-animation-view-example.cpp`: confirms app-facing usage with `Dali::Ui::LottieAnimationView::New`, fluent view-tree chaining, playback controls, loop settings, speed, stop behavior, frame range, render scale, placeholder URL, `ResourceReadySignal`, and `AnimationFinishedSignal`.

Remaining concerns:
- The guide keeps compact examples rather than full runnable setup code, so it does not show adding the view to a window or layout.
- `SetDynamicProperty` callback signature details are not expanded because the public header only exposes `Dali::CallbackBase*` ownership and threading requirements.
