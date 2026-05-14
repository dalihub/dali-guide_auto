# Image View Prose Review

Changed prose:
- Clarified the overview to state that image loading is asynchronous by default.
- Narrowed `Reload()` guidance to reloading the current URL after loader-time option changes.
- Clarified `DownCast()` behavior for non-`ImageView` handles.
- Added source-grounded caution for `SetImageLoadWithViewSize(true)` and aspect-ratio layout.
- Reworded `ResourceReadySignal()` to match the public header: the signal fires when the image has finished loading and is ready to display.
- Reworded `LoadPolicy::IMMEDIATE` to avoid implying that a URL-less view necessarily loads at construction time.
- Reworded `SetFastTrackUpload()` as a request for the fast-track path and listed key implementation constraints.
- Tightened property constants prose to say they are property indices for `GetProperty()` / `SetProperty()` access.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/image-view.h` defines `Dali::Ui::ImageView`, fluent setters, `Reload()`, `ResourceReadySignal()`, `SetImageLoadWithViewSize()`, `SetFastTrackUpload()`, N-patch APIs, and property-index intent.
- `repos/dali-ui/dali-ui-foundation/public-api/image/image-enumerations.h` defines `FittingMode`, `SamplingMode`, `MaskingType`, `LoadPolicy`, and `ReleasePolicy` values used in the guide.
- `repos/dali-ui/dali-ui-foundation/public-api/image-view-properties.h` confirms property names, types, backing indices, and the animatable `PIXEL_AREA`.
- `repos/dali-ui/dali-ui-foundation/integration-api/image-view-impl.cpp` confirms defaults including `LoadPolicy::ATTACHED`, `ReleasePolicy::DETACHED`, `FittingMode::FILL`, `SamplingMode::BOX_THEN_LINEAR`, and `OrientationCorrection` enabled by default.
- `repos/dali-ui/dali-ui-foundation/internal/visuals/image/image-visual.cpp` confirms `Reload()` behavior, view-size loading behavior, and fast-track eligibility constraints.
- Samples under `repos/dali-ui/samples/image-view/` confirm usage patterns for fitting, desired size plus `Reload()`, placeholders and `ResourceReadySignal()`, loading policies, alpha masks, and pixel area.

Remaining concerns:
- Code snippets were preserved as requested and reviewed against public headers, but they were not compiled in this read-only review pass.
- The guide intentionally does not document every default value or every fast-track rejection condition; only the conditions needed to avoid overclaiming are included.
