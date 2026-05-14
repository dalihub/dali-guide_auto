# Label Prose Review

Changed prose:
- Clarified `SetFontVariation(const Dali::String&)` as comma-separated axis/value settings and noted that invalid input leaves existing variation unchanged.
- Refined `SetLineHeight` wording: `RELATIVE` uses the effective scaled font size, and `ABSOLUTE` is a pixel value still affected by font size scale.
- Corrected anchor guidance to say anchors are in markup-enabled label text.
- Updated the anchor example minimally so the text contains `<a href=...>` markup and calls `SetMarkupEnabled(true)`.
- Added a localized note that `AsyncRenderFinishedSignal` delivers async render completion with size including padding.
- Clarified that `GetAsyncLineCount` is updated after async render or async size computation.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/label.h` documents `Label` as a non-editable `View`, `New(const Dali::String&)`, typed setters/getters, `SetMarkupEnabled`, `AnchorClickedSignal`, async signals, `GetAsyncLineCount`, `SetFontVariation`, `SetLineHeight`, and `SetRenderScale`.
- `repos/dali-ui/dali-ui-foundation/public-api/text/label-properties.h` confirms `MARKUP_ENABLED`, `ANCHOR_COLOR`, `ANCHOR_CLICKED_COLOR`, `ASYNC_RENDERING`, and `RENDER_SCALE` property semantics.
- `repos/dali-ui/dali-ui-foundation/internal/text/markup-tags-and-attributes.h` and markup processing sources confirm anchor markup uses an `href` attribute.
- Samples under `repos/dali-ui/samples/text/` confirm view-tree usage, line-height modes, layout direction modes, async rendering toggles, and render-scale examples.

Remaining concerns:
- The examples were not compiled in this read-only review pass.
- The guide intentionally keeps marquee and text-fit APIs out of scope to preserve the existing section structure.
