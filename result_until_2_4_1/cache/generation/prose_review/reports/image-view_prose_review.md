# Image View Prose Review

Changed prose:
- Added that `ImageView` loads asynchronously by default.
- Added `Dali::Ui::ImageView::New(const Dali::String&)` as a construction option.
- Refined desired size wording from generic loader hints to rasterization size hints, including the `0` natural-size behavior.
- Clarified `SetImageLoadWithViewSize` behavior and added the aspect-ratio layout caveat.
- Simplified placeholder guidance so it does not imply ordering is mandatory.
- Corrected fast-track guidance and changed the example from `ReleasePolicy::NEVER` to `ReleasePolicy::DETACHED`.
- Clarified synchronous loading as main-thread loading.
- Added that a non-zero N-patch border activates N-patch rendering.
- Adjusted property table wording for desired width/height and property-index mapping.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/image-view.h`: documents async loading by default, `New()`, `New(const Dali::String&)`, `SetResourceUrl`, `Reload`, `ResourceReadySignal`, desired width/height, `SetImageLoadWithViewSize`, N-patch behavior, and `SetDepthIndex`.
- `repos/dali-ui/dali-ui-foundation/public-api/image-view-properties.h`: documents property names, types, desired-size semantics, `IMAGE_LOAD_WITH_VIEW_SIZE`, `FAST_TRACK_UPLOADING`, `ORIENTATION_CORRECTION`, and `PIXEL_AREA`.
- `repos/dali-ui/dali-ui-foundation/public-api/image/image-enumerations.h`: confirms `FittingMode`, `SamplingMode`, `MaskingType`, `LoadPolicy`, and `ReleasePolicy` meanings.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` and `internal/views/view/view-data-impl.cpp`: confirm `ResourceReadySignalType` is `Signal<void(View)>` and emits a `Dali::Ui::View`.
- `repos/dali-ui/dali-ui-foundation/internal/visuals/image/image-visual.cpp`: confirms fast-track upload is only available under specific conditions including `LoadPolicy::ATTACHED`, `ReleasePolicy::DETACHED`, no synchronous loading, and no alpha mask URL.
- `repos/dali-ui/samples/image-view/image-view-example.cpp`, `image-placeholder-example.cpp`, `image-sampling-desired-size-example.cpp`, and `image-loading-policy-example.cpp`: confirm app-facing sample usage of `ImageView::New`, fluent setters, resource-ready callbacks, desired size, placeholder URLs, and loading-policy controls.

Remaining concerns:
- Code blocks were preserved except for the fast-track release policy value. They were reviewed for public API symbol validity, but not compiled in this pass.
- The guide mentions fast-track eligibility at a practical level; the implementation has additional internal constraints such as URL protocol, cached reload policy, and custom shader state that are intentionally not expanded in the user-facing prose.
