# Image View Prose Review

Changed prose:
- Clarified that `Dali::Ui::ImageView` is a `Dali::Ui::View`.
- Added that `Dali::Ui::ImageView::New(const Dali::String&)` is available.
- Clarified `Reload()` has no effect when no image URL is set.
- Clarified `DownCast()` returns a valid handle only for an `ImageView`.
- Tightened fitting-mode wording: `FILL` is the default and stretches; `OVER_FIT_KEEP_ASPECT_RATIO` crops overflow.
- Changed `SetPixelArea()` wording from “texture coordinates” to normalized coordinates with `[0, 1]` components.
- Added the `SetImageLoadWithViewSize(true)` warning about aspect-ratio layout on the same `ImageView`.
- Corrected orientation correction prose to state that the implementation initializes it to `true`.
- Tightened `ResourceReadySignal()` prose to match “finished loading and ready to display.”
- Replaced example-oriented color-use claims with the documented `UiColor` behavior.
- Qualified `SetFastTrackUpload(true)` as a request that is used only when the load configuration supports it.
- Removed the misleading `Dali::Ui::ImageViewPropertyIndex::PropertyRange` example from the property-index prose.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/image-view.h`: public `ImageView` API, fluent setters, `New()`, `New(const Dali::String&)`, `Reload()`, `DownCast()`, `ResourceReadySignal()`, loading and fitting comments.
- `repos/dali-ui/dali-ui-foundation/public-api/image/image-enumerations.h`: `FittingMode`, `SamplingMode`, `MaskingType`, `LoadPolicy`, and `ReleasePolicy` meanings.
- `repos/dali-ui/dali-ui-foundation/public-api/image-view-properties.h`: property names, types, and property-index membership.
- `repos/dali-ui/dali-ui-foundation/integration-api/image-view-impl.cpp`: defaults for sampling, fitting, load/release policy, orientation correction, placeholder behavior, `Reload()`, `SetImageLoadWithViewSize()`, and fast-track handling.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`: `ResourceReadySignalType` callback type and resource-ready signal behavior.
- `repos/dali-ui/samples/image-view/*.cpp`: app-facing sample usage for `ImageView::New(url)`, `ResourceReadySignal()`, fitting modes, alpha masks, placeholders, pixel area, desired size, load policies, and N-patch controls.

Remaining concerns:
- Code blocks were preserved as much as possible; they are illustrative snippets and may require surrounding app setup, includes, and resource paths in a real application.
- The guide still avoids documenting default values exhaustively, except where a default affects prose accuracy.
