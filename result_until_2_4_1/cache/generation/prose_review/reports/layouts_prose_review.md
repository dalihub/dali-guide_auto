# Layouts Prose Review

Changed prose:
- Changed "`Dali::Ui::Layout` ... owns a layout algorithm" to "owns a layout manager" to match `layout.h`, which says `Layout` owns a `LayoutManager` that defines the algorithm.
- Changed "The parameter type must match the parent layout" to "Use the parameter type for the parent layout" to avoid implying a runtime validation rule while preserving the app-facing guidance.
- Changed "A negative width or height..." to "A width or height of `-1`..." because `absolute-layout-params.h` documents `-1` as using the view's own size, while the implementation treats negative axes with additional `WRAP_CONTENT` / `MATCH_PARENT` behavior.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/layout.h`: `Layout` extends `View` and owns a `LayoutManager`.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`: `View::SetLayoutParams()`, `View::Children()`, and typed `GetLayoutParams<T>()` are public APIs.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/absolute-layout-params.h`: `SetBounds()`, `SetX()`, `SetY()`, `SetWidth()`, `SetHeight()`, `SetFlags()`, and copy construction via `New(const AbsoluteLayoutParams&)` are public.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/layout-types.h`: `AbsoluteLayoutFlags`, `FlexDirection`, `FlexWrap`, `FlexJustify`, `FlexAlign`, `GridLength`, and `LayoutAlignment` values used in the guide are public.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/flex-layout.h` and `flex-layout-params.h`: flex container setters/getters and child params in the examples are public.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/grid-layout.h` and `grid-layout-params.h`: grid definition, spacing, span, alignment, clear, and getter APIs used in the examples are public.
- Samples under `repos/dali-ui/samples/absolutelayout`, `repos/dali-ui/samples/flexlayout`, and `repos/dali-ui/samples/gridlayout` confirm the `View`-based layout tree idiom and the typed layout params usage.

Remaining concerns:
- Code blocks were preserved as requested and were not compiled in this review.
- The guide intentionally keeps `Dali::Ui::StackLayout` out of the main sections because the draft structure targets absolute, flex, and grid layout flows.
