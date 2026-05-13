# Lottie Animation View Prose Review

Changed prose:
- Tightened the overview to match the public header wording: `LottieAnimationView` displays Lottie vector animations and controls playback in a `View` tree.
- Added a localized clarification that `SetDesiredWidth` and `SetDesiredHeight` are rasterization size hints, while layout sizing should use `View` layout APIs.
- Clarified that `LottieAnimationView::Property` constants are aliases of `LottieAnimationViewPropertyIndex`, which also documents property names and types.
- Added source-backed state-query behavior: before a live visual is available, `GetPlayState`, `GetCurrentFrame`, and `GetTotalFrame` report stopped/zero values.
- Changed "splitting one Lottie file into several application states" to the narrower "playing a selected frame range" to avoid overstating behavior.
- Corrected release policy wording to "animation texture is released from the cache" and aligned `DESTROYED`/`NEVER` descriptions with `Image::ReleasePolicy`.
- Added the documented `SetRenderScale` behavior that negative values flip the image.
- Reworded `SetNotifyAfterRasterization` from "enables resource-ready notification" to the more general "controls whether notification is sent after rasterization."
- Clarified `GetContentInfo` and `GetMarkerInfo` map contents: keys are names and values are frame ranges.
- Reworded `SetDynamicProperty` ownership text to name `DynamicPropertyInfo::callback` specifically.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/lottie-animation-view.h`: class description, constructor, typed setters/getters, signal signatures, frame range and marker comments, dynamic property ownership, metadata map descriptions.
- `repos/dali-ui/dali-ui-foundation/public-api/lottie-animation-view-properties.h`: property names, property types, default notes, rasterization and pixel-area behavior.
- `repos/dali-ui/dali-ui-foundation/public-api/image/image-enumerations.h`: `Image::ReleasePolicy` meanings.
- `repos/dali-ui/dali-ui-foundation/public-api/image/lottie-animation-types.h`: `DynamicPropertyInfo::callback` ownership and worker-thread callback warning.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-LottieAnimationView.cpp`: default values and no-visual behavior for playback state, frame counts, release policy, redraw, frame cache, notify-after-rasterization, render scale, and metadata getters.
- `repos/dali-ui/samples/image-view/lottie-animation-view-example.cpp`: app-facing sample usage with `LottieAnimationView`, `View`, fluent setters, playback control, resource-ready signal, and animation-finished signal.

Remaining concerns:
- Code blocks were preserved except for prose around them; they were reviewed against the public API shape but not compiled in this read-only review.
- The guide intentionally avoids documenting every property constant mechanically, because the draft is an application developer guide rather than an API reference.
