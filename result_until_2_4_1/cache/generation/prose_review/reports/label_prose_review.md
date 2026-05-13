# Label Prose Review

Changed prose:
- Clarified `SetFontVariation` settings-string format and avoided implying that every parsed axis is necessarily applied by the selected font.
- Corrected multi-line layout guidance to mention `SetMultiLine(true)` before `SetLineWrapMode`, and updated the wrapping code block with `SetMultiLine(true)`.
- Expanded line-height prose to match `LineHeightMode::RELATIVE`, `LineHeightMode::ABSOLUTE`, `fontSizeScale`, and natural-line-height clamping.
- Tightened `GetLineCount` guidance to say the label width must be resolved, and clarified that `GetAsyncLineCount` comes from the most recent async render or async size computation.
- Clarified anchor prose so anchors are tied to markup processing through `SetMarkupEnabled(true)`.
- Replaced the speculative async-rendering motivation with header-grounded behavior: `SetAsyncRendering` requests async text rendering during relayout, synchronous rendering is the default, and `AsyncRenderFinishedSignal` reports render completion.
- Added the documented `SetRenderScale` constraint that it is valid only with async rendering and must be `1.0f` or greater.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/label.h`: `Label` is a non-editable `View`; `New`, typed fluent setters, `SetMultiLine`, `SetLineWrapMode`, `SetLineHeight`, `SetLayoutDirectionMode`, scale getters, `SetMarkupEnabled`, anchor colors, async rendering, render scale, line-count APIs, and signals are public.
- `repos/dali-ui/dali-ui-foundation/public-api/label.autogen.h`: fluent setters return the child class handle, documents font variation string formats, line-height behavior, async rendering default, and render-scale constraints.
- `repos/dali-ui/dali-ui-foundation/public-api/text/label-properties.h`: confirms `Label::Property` IDs and property meanings, including `RENDER_SCALE`, `ASYNC_RENDERING`, `MARKUP_ENABLED`, and text color properties.
- `repos/dali-ui/samples/text/text-example.cpp`, `text-markup-example.cpp`, `text-scale-example.cpp`, and `text-render-scale-example.cpp`: confirm common app-facing usage with `Label::New`, fluent setters, `SetMultiLine(true)`, async signals, anchor signal callbacks, and render-scale samples.

Remaining concerns:
- Code snippets were reviewed against public API shape but were not compiled in this read-only review pass.
- The guide does not cover marquee, text fit, outline/shadow/underline, mask, or cutout examples beyond property mentions; that is acceptable for a localized prose review but may be expanded in a separate feature-depth pass.
