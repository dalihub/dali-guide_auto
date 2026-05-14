# Animated Image View Prose Review

Changed prose:
- Tightened the overview to say `Dali::Ui::AnimatedImageView` displays animated image resources and provides playback control.
- Changed "is a `Dali::Ui::View`" to "derives from `Dali::Ui::View`" for precise object-model wording.
- Changed `SetResourceUrls` prose from "plays an ordered list" to "configures an ordered list" because playback still depends on `Play`.
- Changed cache wording from "decoded frames are kept" to "frames are kept in cache" to match the public header wording without over-specifying implementation details.
- Added that the placeholder is removed when the main image resource becomes ready.
- Adjusted `LoadPolicy::IMMEDIATE` wording to "loads as soon as the visual is configured" to better match the implementation behavior when the URL or URL array is available.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/animated-image-view.h` documents `AnimatedImageView : public View`, `New`, playback control, frame sequence APIs, load and release policies, masking, placeholder, loading status, and both signals.
- `repos/dali-ui/dali-ui-foundation/public-api/animated-image-view-properties.h` documents property constants and property meanings, including `FRAME_DELAY`, `CACHE_SIZE`, `PLACEHOLDER_IMAGE`, and `PIXEL_AREA`.
- `repos/dali-ui/dali-ui-foundation/integration-api/animated-image-view-impl.cpp` shows `SetResourceUrl` clears URL arrays, `SetResourceUrls` clears the single URL, `SetLoadPolicy(IMMEDIATE)` updates the visual when a URL or URL array is available, and placeholder visuals are removed after the main image reaches `ResourceStatus::READY`.
- `repos/dali-ui/dali-ui-foundation/public-api/image/image-enumerations.h` confirms `FittingMode`, `MaskingType`, `SamplingMode`, `LoadPolicy`, and `ReleasePolicy` values used in the guide.
- `repos/dali-ui/samples/image-view/animated-image-view-example.cpp` confirms app-facing usage of `Play`, `Pause`, `Stop`, `JumpToFrame`, `SetResourceUrls`, `SetFrameDelay`, `SetFrameSpeedFactor`, `SetStopBehavior`, `ResourceReadySignal`, and `AnimationFinishedSignal`.

Remaining concerns:
- Code examples were preserved as requested and checked against public API names, but they were not compiled in this read-only review environment.
- The guide intentionally avoids exhaustive defaults; defaults are present in implementation but not necessary for the current app-facing flow.
