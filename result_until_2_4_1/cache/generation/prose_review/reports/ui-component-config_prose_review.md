# UiComponentConfig Prose Review

Changed prose:
- Clarified that `UiComponentConfig::New()` initializes defaults inherited from `UiConfig`, and listed defaults evidenced by `ui-config.h` and `ui-config-impl.cpp`.
- Replaced the startup example that only created an unused handle with an app-startup configuration example using inherited fluent setters and `Apply()`.
- Added that `UiComponentConfig` should be used for dali-ui-components applications, while `Dali::Ui::View` remains the UI structure object model.
- Updated handle lifetime prose to include both copy and move operations, matching the public header.
- Clarified that moved or shared setup should happen before `Apply()` freezes configuration values.
- Corrected `DownCast` examples to accept `Dali::BaseHandle`, matching the public signature `UiComponentConfig DownCast(BaseHandle handle)`.
- Adjusted generated chaining prose: `ui-component-config.autogen.h` defines `DALI_UI_CHAIN_UICOMPONENTCONFIG_METHODS`, while the public class exposes inherited `UiConfig` fluent setters.

Source evidence:
- `repos/dali-ui/dali-ui-components/public-api/ui-component-config.h`: `UiComponentConfig` extends `UiConfig`, declares `New()`, `DownCast(BaseHandle)`, default/copy/move handle operations, and generated chain support.
- `repos/dali-ui/dali-ui-components/public-api/ui-component-config.cpp`: `New()` creates `UiComponentConfigImpl`; `DownCast()` uses the supplied `BaseHandle`.
- `repos/dali-ui/dali-ui-components/integration-api/ui-component-config-impl.cpp`: component config implementation inherits `UiConfigImpl` defaults.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-config.h`: `Apply()` freezes config; setters must be called before `Apply()`; `UiConfig` exposes inherited configuration setters.
- `repos/dali-ui/dali-ui-foundation/integration-api/ui-config-impl.cpp`: confirms defaults including scaling factor, DPI, baseline DPI, font size, and text color.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-config.autogen.h`: confirms generated fluent setter methods such as `SetDpi()` and `SetScalingFactor()`.
- `repos/dali-ui/wiki/Configuration.md`: confirms dali-ui startup configuration pattern and the recommendation to use `UiComponentConfig` when using dali-ui-components.

Remaining concerns:
- The public `ui-component-config.h` comment shows `SetDefaultFontSize()` and `SetDefaultTextColor()` in a chain, but `ui-config.autogen.h` in this repository does not currently generate chain wrappers for those setters. The revised guide avoids using those methods in a `UiComponentConfig` fluent chain.
- The wiki configuration table appears to mention `SetMinLongPressKeyCount(uint32_t)`, while the public header uses `SetKeyLongPressThreshold(uint32_t)`. This guide does not rely on that wiki row.
