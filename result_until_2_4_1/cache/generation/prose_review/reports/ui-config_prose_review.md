# Ui Config Prose Review

Changed prose:
- Clarified that `Dali::Ui::UiConfig` defines defaults used by dali-ui, and that `Apply()` should happen before UiConfig-dependent features are used.
- Added the `Apply()` exactly-once requirement and kept the existing startup flow.
- Narrowed scaling wording: `SetScalingFactor()` applies to `spx` and `sdp`; `GetDpiFactor()` and `GetScaledDpiFactor()` are post-`Apply()` values.
- Changed placeholder text wording to match the public API: it controls whether placeholder text is shown when the control has focus.
- Changed `Label` to `Dali::Ui::Label`.
- Clarified keyboard prose: key click execution, repeated key press event threshold for long press, and tap time in milliseconds.
- Changed broken image prose to say fallbacks are for image views when image loading fails.
- Changed marquee getter prose from “effective defaults” to “configured defaults.”
- Narrowed `DALI_UI_CHAIN_UICONFIG_METHODS` wording to the chain-generated `UiConfig` setters, because the macro does not wrap every `UiConfig` setter.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-config.h`: documents `UiConfig`, `Apply()`, the freeze behavior, `GetDpiFactor()`, `GetScaledDpiFactor()`, text defaults, input defaults, broken image types, marquee defaults, and chaining setters.
- `repos/dali-ui/dali-ui-foundation/integration-api/ui-config-manager-impl.cpp`: `Initialize()` asserts it is called only once, stores the config, and freezes it.
- `repos/dali-ui/dali-ui-foundation/integration-api/ui-config-impl.cpp`: confirms default values, cached DPI factor formulas, setter freeze assertions, default execution key predicate, default text/marquee/input values, and broken image URL storage.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-config.autogen.h`: confirms which setters `DALI_UI_CHAIN_UICONFIG_METHODS` wraps.
- `repos/dali-ui/dali-ui-foundation/public-api/key-click-policy.h`: confirms `KeyClickPolicy` values and key-input purpose.
- `repos/dali-ui/samples/text/text-render-scale-example.cpp`: confirms app-facing marquee setter usage with `Dali::Ui::Text` enums.

Remaining concerns:
- The public header comment says passing `nullptr` to `SetExecutionKeyPredicate()` restores the default predicate, but the current implementation asserts that the predicate is non-null. The guide avoids recommending `nullptr` until the API and implementation agree.
- The guide keeps the existing section structure and does not add a separate section for `SetDefaultInteractionEffect()`, although it is part of the public `UiConfig` surface.
