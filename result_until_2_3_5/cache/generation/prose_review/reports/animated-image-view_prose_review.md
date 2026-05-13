# Animated Image View Prose Review

Changed prose:
- Clarified the overview to include GIF/WebP files and frame image sequences, matching `New`, `SetResourceUrl`, and `SetResourceUrls`.
- Renamed the `StopAtConfiguredFrame` snippet function to `StopAnimation` because `Stop()` does not take or configure a frame by itself.
- Tightened `SetResourceUrls` prose to say a URL array overrides a single URL source, and changed the switch-back wording to "clear the URL vector".
- Refined playback tuning text: `SetFrameSpeedFactor` slow-down range is `0.0f` to `1.0f`, and `SetFrameDelay(-1)` uses the embedded file delay.
- Clarified `SetBatchSize` and `SetCacheSize` behavior as pre-loaded batches and retained cache frames.
- Changed completion-signal wording to "completes all configured loops".
- Changed sizing text from general "loader hints" to "decoding hints".
- Reworded `SetImageColor` from "tint" to "apply a color" and corrected the sample from `UiColor(0xFFFFFFFF)` to explicit RGBA construction.
- Reworded the `AnimatedImageViewPropertyIndex` paragraph to describe it as a shared index definition, not an app-facing API to depend on.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/animated-image-view.h` defines `AnimatedImageView` as a `View`, `New`, typed fluent setters/getters, playback controls, `SetResourceUrls`, `ResourceReadySignal`, and `AnimationFinishedSignal`.
- `repos/dali-ui/dali-ui-foundation/public-api/animated-image-view-properties.h` documents property names, property types, `IMAGE_URLS` overriding `IMAGE`, frame delay `-1`, and `AnimatedImageViewPropertyIndex`.
- `repos/dali-ui/dali-ui-foundation/public-api/image/image-enumerations.h` confirms `FittingMode`, `SamplingMode`, `LoadPolicy`, `ReleasePolicy`, and `MaskingType` values used in examples.
- `repos/dali-ui/dali-ui-foundation/public-api/image/animated-image-enumerations.h` confirms `PlayState` and `StopBehavior` values.
- `repos/dali-ui/samples/image-view/animated-image-view-example.cpp` shows frame URL arrays, clearing `SetResourceUrls(Dali::Vector<Dali::String>{})` before returning to a single URL, playback controls, loop count, speed factor, frame delay, and signals.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-AnimatedImageView.cpp` confirms defaults and getter/setter behavior for resource URLs, loop count, frame delay, cache and batch size, stop behavior, synchronous loading, and signals.

Remaining concerns:
- The guide keeps the original section structure and most code blocks as requested. Snippets are API-focused and may still require normal application-level includes or surrounding setup in a full compiled sample.
