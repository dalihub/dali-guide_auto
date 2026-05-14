# Image Prose Review

## Summary

The draft was reviewed line by line against public headers (`image-view.h`, `animated-image-view.h`, `image-enumerations.h`, `animated-image-enumerations.h`) and sample files (`image-loading-policy-example.cpp`, `animated-image-view-example.cpp`). **No corrections were required.**

## Verification Results

### API Names and Signatures
All API names, method signatures, and enum values in the draft were verified against the public headers:

| API | Header | Status |
|-----|--------|--------|
| `ImageView::New()` | `image-view.h` | ✓ Correct |
| `SetResourceUrl()` | `image-view.h` | ✓ Correct |
| `SetFittingMode()` | `image-view.h` | ✓ Correct |
| `SetSamplingMode()` | `image-view.h` | ✓ Correct |
| `SetLoadPolicy()` | `image-view.h` | ✓ Correct |
| `SetReleasePolicy()` | `image-view.h` | ✓ Correct |
| `SetSynchronousLoading()` | `image-view.h` | ✓ Correct |
| `SetFastTrackUpload()` | `image-view.h` | ✓ Correct |
| `SetPixelArea()` | `image-view.h`, `animated-image-view.h` | ✓ Correct |
| `SetAlphaMaskUrl()` | `image-view.h`, `animated-image-view.h` | ✓ Correct |
| `SetCropToMask()` | `image-view.h`, `animated-image-view.h` | ✓ Correct |
| `SetMaskingMode()` | `image-view.h`, `animated-image-view.h` | ✓ Correct |
| `AnimatedImageView::New()` | `animated-image-view.h` | ✓ Correct |
| `Play()`, `Pause()`, `Stop()` | `animated-image-view.h` | ✓ Correct |
| `SetLoopCount()` | `animated-image-view.h` | ✓ Correct |
| `JumpToFrame()` | `animated-image-view.h` | ✓ Correct |
| `GetCurrentFrame()`, `GetTotalFrame()` | `animated-image-view.h` | ✓ Correct |
| `SetFrameSpeedFactor()` | `animated-image-view.h` | ✓ Correct |
| `SetStopBehavior()` | `animated-image-view.h` | ✓ Correct |
| `SetBatchSize()`, `SetCacheSize()`, `SetFrameDelay()` | `animated-image-view.h` | ✓ Correct |
| `SetResourceUrls()` | `animated-image-view.h` | ✓ Correct |
| `ResourceReadySignal()` | `image-view.h`, `animated-image-view.h` | ✓ Correct |
| `AnimationFinishedSignal()` | `animated-image-view.h` | ✓ Correct |
| `GetLoadingStatus()` | `image-view.h`, `animated-image-view.h` | ✓ Correct |
| `Reload()` | `image-view.h` | ✓ Correct |

### Enum Values
All enum values in code examples were verified against `image-enumerations.h` and `animated-image-enumerations.h`:

| Enum | Values | Status |
|------|--------|--------|
| `FittingMode` | `FIT_KEEP_ASPECT_RATIO`, `FILL`, `OVER_FIT_KEEP_ASPECT_RATIO`, `CENTER` | ✓ Correct |
| `SamplingMode` | `NEAREST`, `LINEAR`, `LANCZOS` | ✓ Correct |
| `LoadPolicy` | `IMMEDIATE`, `ATTACHED` | ✓ Correct |
| `ReleasePolicy` | `DETACHED`, `DESTROYED`, `NEVER` | ✓ Correct |
| `MaskingType` | `MASKING_ON_LOADING` | ✓ Correct |
| `StopBehavior` | `CURRENT_FRAME`, `FIRST_FRAME`, `LAST_FRAME` | ✓ Correct |

### Prose Accuracy
All natural-language paragraphs were verified against header documentation and sample file comments:

- Default fitting mode (`FILL`) matches header documentation
- Asynchronous loading behavior matches header documentation
- Signal emission timing matches header documentation
- Stop behavior descriptions match enum documentation
- Loop count semantics (including `-1` for infinite) match header documentation
- Frame speed factor behavior matches header documentation

### Code Examples
All code examples were verified against sample files for idiomatic usage:

- Fluent chaining pattern matches samples
- `ConnectionTracker` inheritance for signal handling matches samples
- `DownCast()` usage matches samples
- `Dali::Vector<Dali::String>` usage for URL arrays matches samples

## Remaining Concerns

None. The draft accurately reflects the public API surface and follows dali-ui idioms.
