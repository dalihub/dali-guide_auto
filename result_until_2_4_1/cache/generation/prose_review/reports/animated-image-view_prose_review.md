# Animated Image View Prose Review

Changed prose:
- Clarified the overview to say `Dali::Ui::AnimatedImageView` displays animated resources such as GIF or WebP files.
- Tightened `SetBatchSize` and `SetCacheSize` wording to match the header: batch size pre-loads frames per batch, cache size keeps frames in cache.
- Added that `SetResourceUrl` clears a URL sequence and `SetResourceUrls` clears the single URL.
- Clarified `SetDesiredWidth` and `SetDesiredHeight` as pixel decode-size hints.
- Changed "cache lifetime" to "resource lifetime" for `SetLoadPolicy` and `SetReleasePolicy`.
- Clarified `SetPixelArea` coordinates as normalized `[0, 1]` components.
- Clarified `ResourceReadySignal` wording to match the public header: loading has finished and the resource is ready to display.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/animated-image-view.h` confirms `AnimatedImageView` derives from `Dali::Ui::View`, exposes `New`, fluent typed setters, playback methods, loading methods, masking methods, `GetLoadingStatus`, `ResourceReadySignal`, and `AnimationFinishedSignal`.
- `repos/dali-ui/dali-ui-foundation/public-api/animated-image-view-properties.h` confirms property names, types, and `PIXEL_AREA` as normalized sub-region data.
- `repos/dali-ui/dali-ui-foundation/integration-api/animated-image-view-impl.cpp` confirms `SetResourceUrl` clears `mUrls`, `SetResourceUrls` clears `mUrl`, and batch/cache values below `1` are clamped internally.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-AnimatedImageView.cpp` confirms defaults and typed getter/setter behavior for loop count, frame delay, batch size, cache size, stop behavior, synchronous loading, pixel area, and URL arrays.
- `repos/dali-ui/samples/image-view/animated-image-view-example.cpp`, `image-alpha-mask-example.cpp`, and `image-loading-policy-example.cpp` confirm app-facing usage with dali-ui view-tree idioms, fluent chaining, signals, URL arrays, playback, masks, and loading policies.

Remaining concerns:
- Code blocks were preserved because their API usage matches the public header. They were not compiled in this review environment.
- The guide intentionally keeps `Dali::Ui::AnimatedImageViewPropertyIndex` as backing-index context rather than recommending it for normal application code.
