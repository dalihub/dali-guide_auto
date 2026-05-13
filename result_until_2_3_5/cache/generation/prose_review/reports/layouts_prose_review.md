# Layouts Prose Review

Changed prose:
- Replaced "Each layout is a `Dali::Ui::View`" with "Each layout derives from `Dali::Ui::View`" to match the public inheritance model.
- Replaced the generic guidance about "storing layout data in untyped properties" with guidance to attach typed layout params through `Dali::Ui::View::SetLayoutParams`.
- Narrowed the `Dali::Ui::AbsoluteLayoutParams::SetFlags` sentence from "whether bounds are interpreted proportionally" to "which bounds components are interpreted proportionally."
- Added `Dali::Ui::StackLayoutParams` to the typed layout parameter paragraph, because stack layout has its own public parameter handle.
- Reworded "owned application-facing APIs" to "public handle types" for clearer, source-grounded language.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/layout.h`: `Dali::Ui::Layout` derives from `Dali::Ui::View`.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/layout-params.h`: `Dali::Ui::LayoutParams` is the common base for `AbsoluteLayoutParams`, `FlexLayoutParams`, `GridLayoutParams`, and `StackLayoutParams`, and is used with `View::SetLayoutParams()` / `View::GetLayoutParams()`.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/absolute-layout-params.h`: absolute params expose `SetBounds`, `SetX`, `SetY`, `SetWidth`, `SetHeight`, `SetFlags`, and matching getters.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/layout-types.h`: `AbsoluteLayoutFlags` has per-axis proportional flags plus `POSITION_PROPORTIONAL`, `SIZE_PROPORTIONAL`, and `ALL`.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/flex-layout.h` and `flex-layout-params.h`: flex container and child setters/getters in the examples are public APIs.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/grid-layout.h`, `grid-layout-params.h`, and `layout-types.h`: grid track, `GridLength`, count, definition inspection, clear, and grid params APIs in the examples are public APIs.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/stack-layout.h` and `stack-layout-params.h`: stack layout and stack params are public APIs.
- Samples under `repos/dali-ui/samples/absolutelayout`, `repos/dali-ui/samples/flexlayout`, and `repos/dali-ui/samples/gridlayout` show the same View-based layout tree and typed `SetLayoutParams` usage.

Remaining concerns:
- I preserved the section structure and code blocks as requested. The guide still does not include a dedicated stack-layout example, even though `StackLayout` is listed in the container choice section.
- The code snippets are source-shaped against headers and samples, but I did not compile them in this read-only review pass.
