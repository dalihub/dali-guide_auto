# Ui Config Prose Review

Changed prose:
- Replaced “before the `Dali::Ui::View` tree is built” with “before app code uses dali-ui configuration-dependent features” to match the initialization guard in `UiConfigManagerImpl`, which mentions the main loop and static/global initializers rather than only view construction.
- Tightened startup guidance to say `Dali::Ui::UiConfig::Apply()` is called exactly once, typically before the application main loop starts.
- Changed scaling prose from “scalable dali-ui units” to `spx` and `sdp`, and stated that computed DPI factor getters require `Apply()`.
- Adjusted text default wording to “text elements” and `Dali::Ui::Label` async rendering to match public header wording.
- Expanded input prose to cover `SetKeyLongPressThreshold()` and `SetTapRecognizerTime()`, because the example uses both.
- Added `SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE)` and `GetKeyClickPolicy()` in the input examples so the code matches the surrounding `SetKeyClickPolicy()` prose.
- Added the plain-function-pointer limitation for `Dali::Ui::ExecutionKeyPredicate`.
- Added units for marquee speed, delay, and gap.
- Clarified `DownCast()` returns an uninitialized handle when the source handle is not a `UiConfig`.
- Reworked the `ui-config.autogen.h` prose locally to avoid implying the macro creates or initializes a derived config object.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-config.h`: public API signatures and comments for `UiConfig::New()`, `Apply()`, setters, getters, `BrokenImageType`, `ExecutionKeyPredicate`, marquee settings, text defaults, focus settings, and `DownCast()`.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-config.autogen.h`: `DALI_UI_CHAIN_UICONFIG_METHODS` forwards selected setters and returns `ChildClass&`.
- `repos/dali-ui/dali-ui-foundation/integration-api/ui-config-manager-impl.cpp`: `Initialize()` asserts that config is initialized only once and freezes the config; the not-initialized message says to call `UiConfig::New().Apply()` in `main()` before the application main loop.
- `repos/dali-ui/dali-ui-foundation/integration-api/ui-config-impl.cpp`: defaults, freeze behavior, cached DPI factors, setter assertions after freeze, and getter implementations.
- `repos/dali-ui/dali-ui-foundation/public-api/key-click-policy.h`: `KeyClickPolicy` values and key-click timing semantics.
- `repos/dali-ui/samples/text/text-example.cpp` and `repos/dali-ui/samples/text/text-marquee-example.cpp`: app startup examples using `UiConfig::New()` with fluent setters followed by `Apply()`.

Remaining concerns:
- The public header comment says passing `nullptr` to `SetExecutionKeyPredicate()` restores the default predicate, but the implementation currently asserts that the predicate is non-null. The guide avoids documenting `nullptr` behavior until that discrepancy is resolved.
- The `ProductUiConfig` snippet is intentionally limited to the macro shape. A complete derived config type would need valid construction around a `Dali::Ui::UiConfig` implementation handle, which is outside the app-facing guide scope.
