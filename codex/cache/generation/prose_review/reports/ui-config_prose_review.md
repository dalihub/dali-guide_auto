# Ui Config Prose Review

Changed prose:
- Clarified that `UiConfig` is applied once, and that `Apply()` should be called before the application main loop starts and before code uses configured dali-ui defaults.
- Replaced the softer “setters are no longer intended” wording with the source-backed assertion behavior after `Apply()`.
- Refined the DPI/unit paragraph so `SetDpi()`, `SetBaselineDpi()`, and `SetScalingFactor()` match the documented `dp`, `spx`, and `sdp` behavior.
- Tightened text default wording from broad “text controls” to text elements and named `Dali::Ui::Label` for label async rendering.
- Clarified `SetKeyClickPolicy()` as applying to D-Pad, remote, or keyboard interaction, matching `key-click-policy.h`.
- Narrowed broken image wording to image views, matching the `SetBrokenImageUrl()` public API comment.
- Adjusted marquee prose to avoid implying all label motion behavior, keeping it specific to marquee defaults.
- Revised the `DALI_UI_CHAIN_UICONFIG_METHODS` example so the derived config wraps a `UiConfigImpl`, following the repository sample pattern instead of calling setters on an uninitialized handle.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-config.h` documents `UiConfig`, `Apply()`, immutable-after-init behavior, setter/getter signatures, DPI factor semantics, broken image types, focus/text/marquee APIs, and `DALI_UI_CHAIN_UICONFIG_METHODS`.
- `repos/dali-ui/dali-ui-foundation/integration-api/ui-config-impl.cpp` confirms default values, frozen setter assertions, cached `dpi / baselineDpi` and scaled DPI factors, default execution key predicate, and getter behavior.
- `repos/dali-ui/dali-ui-foundation/integration-api/ui-config-manager-impl.cpp` confirms `Apply()` initializes the global config once and the user-facing guidance to call `UiConfig::New().Apply()` before the application main loop starts.
- `repos/dali-ui/dali-ui-foundation/public-api/unit.h` confirms `spx`, `dp`, and `sdp` unit dependencies on scaling factor, DPI, and baseline DPI.
- `repos/dali-ui/dali-ui-foundation/public-api/key-click-policy.h` confirms `KeyClickPolicy` is for D-Pad, remote, or keyboard interactions.
- `repos/dali-ui/samples/text/text-example.cpp` and `repos/dali-ui/samples/text/text-marquee-example.cpp` confirm fluent startup usage of `UiConfig::New()` with text and marquee setters followed by `Apply()`.
- `repos/dali-ui/samples/color-controls/test-config.h` and `repos/dali-ui/samples/color-controls/test-config.cpp` confirm the derived configuration pattern used with `DALI_UI_CHAIN_UICONFIG_METHODS`.

Remaining concerns:
- The public header comment says passing `nullptr` to `SetExecutionKeyPredicate()` restores the default predicate, but the implementation asserts that the predicate must not be null. The revised guide avoids documenting `nullptr` behavior.
- `SetBaselineDpi()` implementation appears to assert the current baseline DPI rather than the incoming `baselineDpi`; the guide does not expose this implementation detail.
