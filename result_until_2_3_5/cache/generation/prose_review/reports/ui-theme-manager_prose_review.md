# Ui Theme Manager Prose Review

Changed prose:
- Replaced "global styling service" with "theme-query service" to avoid implying that `Dali::Ui::UiThemeManager` performs style or color lookup directly.
- Changed the move-semantics guidance from "transferring ownership" to "transferring a local handle value" because `Dali::Ui::UiThemeManager` is a handle wrapper, not an exclusive ownership object.

Changed code examples:
- Corrected the `Dali::ConnectionTracker` include from `<dali/public-api/object/connection-tracker.h>` to `<dali/public-api/signals/connection-tracker.h>`.
- Corrected the `Dali::String` empty check from `mThemeId.empty()` to `mThemeId.Empty()`.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-theme-manager.h` documents `Dali::Ui::UiThemeManager` as a handle-based singleton, exposes `Get`, `DownCast`, `GetCurrentThemeId`, `ThemeChangedSignal`, and states that color lookup is through `UiColorManager`, not this class.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-theme-manager.h` defines `ThemeChangedSignalType` as `Signal<void()>` and documents an empty theme ID when no theme is active.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-theme-manager.cpp` confirms `Get`, `DownCast`, `GetCurrentThemeId`, and `ThemeChangedSignal` forward to the implementation.
- `repos/dali-ui/dali-ui-foundation/integration-api/ui-theme-manager-impl.cpp` confirms singleton registration, loader-backed theme ID access, and signal emission through the manager's theme-changed signal.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-UiThemeManager.cpp` confirms `Get` returns a valid singleton handle, repeated `Get` calls compare equal, copy/move operations work as handle operations, `DownCast` handles valid and empty handles, and `ThemeChangedSignal` is connectable.
- `repos/dali-core/dali/public-api/common/dali-string.h` shows `Dali::String` uses `Empty()` for empty checks.

Remaining concerns:
- The examples are guide-style snippets and were not compiled as standalone translation units.
- The guide intentionally does not describe `UiColorManager` usage beyond noting that direct color lookup is outside `Dali::Ui::UiThemeManager`.
