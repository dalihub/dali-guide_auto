# Interactive View Prose Review

Changed prose:
- Replaced "The handle is a `Dali::Ui::View`" with "`Dali::Ui::InteractiveView` derives from `Dali::Ui::View`" to state the object model precisely.
- Clarified that `SetPseudoDisabled()` is separate from clickability and that click activation should be disabled explicitly when the view should not respond to clicks.
- Added a `MenuItemView::DownCast(Dali::BaseHandle)` helper in the custom handle example so the later `MenuItemView::DownCast(view)` call returns the derived handle type.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-view.h` defines `class InteractiveView : public View`, `InteractiveView::New()`, `InteractiveView::DownCast()`, typed setters, signal accessors, and connection helpers.
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-view.autogen.h` defines `DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS`, including derived-return chaining for setters and signal connection helpers.
- `repos/dali-ui/dali-ui-foundation/integration-api/interactive-trait-impl.cpp` stores `mPseudoDisabled` and `mClickable` separately, emits `PseudoDisabledChangedSignal()`, and uses clickability for tap/key click decisions.
- `repos/dali-ui/dali-ui-foundation/public-api/key-click-policy.h` documents `ON_RELEASE`, `ON_PRESS`, and `DISABLED` for D-Pad, remote, and keyboard interactions.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-InteractiveView.cpp` verifies defaults, setter/getter behavior, signal signatures, chaining helpers, and long-press suppression of later click.
- `repos/dali-ui/samples/attachment/my-button-example.cpp` shows the public pattern for a custom `InteractiveView` handle with a derived `DownCast()` and `DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS`.

Remaining concerns:
- The examples are app-facing and use public symbols, but they are illustrative snippets rather than complete buildable translation units with includes and namespace setup.
