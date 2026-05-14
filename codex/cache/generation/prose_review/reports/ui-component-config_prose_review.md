# Ui Component Config Prose Review

Changed prose:
- Reframed the overview from "component-level UI setup in a `Dali::Ui::View`-based application" to "default values used by dali-ui-components" because the public header describes `UiComponentConfig` as configuration for dali-ui-components default values and as a placeholder for component-specific configuration.
- Clarified that `Dali::Ui::UiComponentConfig::New` returns an initialized handle with inherited `Dali::Ui::UiConfig` defaults, rather than implying a per-`View` object that lives alongside the UI tree.
- Changed "transfers ownership" wording to "returns or stores the handle" to better match DALi shared handle semantics.
- Replaced `decltype(handle)` in one example with `Dali::BaseHandle` so the snippet matches the public `DownCast(BaseHandle handle)` signature without relying on an undeclared outer symbol.
- Tightened the generated-chain-method section: `ui-component-config.autogen.h` defines `DALI_UI_CHAIN_UICOMPONENTCONFIG_METHODS` as support for inherited `Dali::Ui::UiConfig` chain methods, while application code should use the typed `Dali::Ui::UiComponentConfig` API.

Source evidence:
- `repos/dali-ui/dali-ui-components/public-api/ui-component-config.h` declares `class DALI_UI_API UiComponentConfig : public UiConfig`.
- The same header describes `UiComponentConfig` as "Configuration for dali-ui-components default values" and says it extends `UiConfig` as a placeholder for component-specific configuration.
- `UiComponentConfig::New()` is documented as creating a new `UiComponentConfig` with default values and inheriting `UiConfig` defaults.
- `UiComponentConfig::DownCast(BaseHandle handle)` is the public downcast API.
- `repos/dali-ui/dali-ui-components/public-api/ui-component-config.autogen.h` defines `DALI_UI_CHAIN_UICOMPONENTCONFIG_METHODS(ChildClass)` in terms of `DALI_UI_CHAIN_UICONFIG_METHODS(ChildClass)`.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-config.autogen.h` shows generated fluent setters such as `SetScalingFactor`, `SetDpi`, and other inherited configuration methods returning `ChildClass&`.

Remaining concerns:
- No feature-specific samples or UTCs were available in the context pack, so the review relied mainly on public headers and implementation files.
- The guide intentionally preserves the original section structure and most code blocks, but richer startup examples using inherited setters and `Apply()` could be added in a later pass if section restructuring is allowed.
