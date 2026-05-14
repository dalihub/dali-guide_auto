# Layouts Prose Review

Changed prose:
- Clarified that layout containers arrange `Dali::Ui::View` children, and that `Dali::Ui::Layout` is both the public base class for layout containers and a `Dali::Ui::View`.
- Tightened `Dali::Ui::FlexLayout` wording to match the header’s CSS Flexbox description.
- Added `Dali::Ui::View::SetLayoutParams` guidance for `Dali::Ui::FlexLayoutParams` and `Dali::Ui::AbsoluteLayoutParams`.
- Adjusted `Dali::Ui::FlexLayoutParams::SetFlexBasis` wording to say the basis applies before grow or shrink.
- Clarified that `Dali::Ui::AbsoluteLayoutFlags` can apply per position or size component.
- Added a localized `Dali::Ui::GridLayoutParams` example for row, column, span, alignment, `SetLayoutParams`, and `Add`.
- Revised the integration-types section to focus on integration-header implementation and manager types, and to avoid presenting internal `LayoutControllerImpl` details as app-facing layout objects.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/layout.h`: `Dali::Ui::Layout` derives from `Dali::Ui::View`.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/flex-layout.h`: flex direction, wrap, justify, align items, and align content setters/getters; CSS Flexbox description.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/flex-layout-params.h`: flex grow, shrink, basis, align-self, and `View::SetLayoutParams` usage.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/absolute-layout-params.h`: bounds, x, y, width, height, flags, and `View::SetLayoutParams` usage.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/layout-types.h`: `LayoutRect`, `GridLength`, flex enums, `AbsoluteLayoutFlags`, bitwise operators, and `LayoutAlignment`.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/grid-layout.h`: row/column definitions, clear methods, spacing, and getters.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/grid-layout-params.h`: row, column, span, alignment params and `View::SetLayoutParams` usage.
- Samples under `repos/dali-ui/samples/flexlayout`, `repos/dali-ui/samples/gridlayout`, and `repos/dali-ui/samples/absolutelayout` confirm `View::SetLayoutParams` and `Layout::Add` usage patterns.

Remaining concerns:
- The guide still uses compact configuration snippets rather than full runnable samples. This matches the existing draft structure and the localized-edit constraint.
