# Ui Component Config Prose Review

Changed prose:
- Revised the overview to state that `Dali::Ui::UiComponentConfig` configures dali-ui-components defaults and extends `Dali::Ui::UiConfig`, rather than describing it only as a pre-view-tree handle.
- Tightened startup guidance to avoid implying that the API is tied directly to constructing a `Dali::Ui::View` tree. The revised text says to create it before configuration-dependent `Dali::Ui::View` behavior is used.
- Clarified generated chain support: `DALI_UI_CHAIN_UICOMPONENTCONFIG_METHODS` expands to inherited `Dali::Ui::UiConfig` chain-method support.
- Reworded the generic-handle paragraph to avoid implying use of internal `Integration` APIs.
- Added the missing `<utility>` include to the `ConfigRegistry` example because it uses `std::move`.
- Replaced the final generated-chain example with a minimal startup-style chain using public inherited setters and `Apply()`.

Source evidence:
- `repos/dali-ui/dali-ui-components/public-api/ui-component-config.h` documents `Dali::Ui::UiComponentConfig` as "Configuration for dali-ui-components default values" and says it extends `UiConfig`.
- `repos/dali-ui/dali-ui-components/public-api/ui-component-config.cpp` shows `Dali::Ui::UiComponentConfig::New()` creates a `UiComponentConfigImpl` and returns an initialized handle.
- `repos/dali-ui/dali-ui-components/public-api/ui-component-config.cpp` implements `Dali::Ui::UiComponentConfig::DownCast()` with a dynamic cast from `BaseHandle`.
- `repos/dali-ui/dali-ui-components/public-api/ui-component-config.autogen.h` defines `DALI_UI_CHAIN_UICOMPONENTCONFIG_METHODS(ChildClass)` as `DALI_UI_CHAIN_UICONFIG_METHODS(ChildClass)`.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-config.h` documents `Apply()` as the global configuration application point and documents setters such as `SetScalingFactor()` and `SetDpi()`.

Remaining concerns:
- No sample or UTC specifically exercises `Dali::Ui::UiComponentConfig`; available examples mainly use `Dali::Ui::UiConfig::New().Apply()`.
- The public `UiComponentConfig` header includes a richer inherited-configuration example, but component-specific properties are currently absent, so the guide should stay narrow until such properties are added.
