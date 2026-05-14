# Ui Theme Manager Prose Review

Changed prose:
- Changed the overview from reacting when the "platform theme changes" to reacting when the "current theme changes" to stay within the public `UiThemeManager` contract.
- Added a localized note that color lookup belongs to `Dali::Ui::UiColorManager`, not `Dali::Ui::UiThemeManager`, matching the public header guidance.
- Kept the existing section structure and examples, with no full rewrite.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-theme-manager.h` defines `Dali::Ui::UiThemeManager`, `Get()`, `DownCast()`, `GetCurrentThemeId()`, `ThemeChangedSignal()`, copy/move operations, and `ThemeChangedSignalType = Signal<void()>`.
- The same header states that `UiThemeManager` exposes theme ID and theme change notification, while color lookups are accessed through `UiColorManager`.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-theme-manager.cpp` confirms forwarding for `GetCurrentThemeId()` and `ThemeChangedSignal()`.
- `repos/dali-ui/dali-ui-foundation/integration-api/ui-theme-manager-impl.cpp` confirms the manager forwards loader theme changes by emitting its own `mThemeChangedSignal`.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-UiThemeManager.cpp` confirms default construction is uninitialized, `Get()` returns a usable singleton, repeated `Get()` handles compare equal, copy/move behavior is supported, `DownCast()` works from `BaseHandle`, and `ThemeChangedSignal()` is connectable.

Code/example corrections:
- Replaced `std::string` storage and lowercase `clear()` / `empty()` calls with `Dali::String`, `Clear()`, and `Empty()` because `GetCurrentThemeId()` returns `Dali::String`.
- Changed the `DownCast()` helper parameter from abbreviated `auto` to `Dali::BaseHandle`, matching the public API signature.

Remaining concerns:
- The draft remains an application-developer guide and intentionally does not document integration-level `ThemeLoaderInterface` setup or `UiConfigImpl::CreateThemeLoader()`.
- The examples assume the application has initialized the dali-ui configuration path normally before querying theme loader-backed state.
