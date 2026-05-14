# Scroll Bar Prose Review

Changed prose:
- Replaced "scrollable dali-ui content" with "scrollable dali-ui views" to match the `View`-based public API.
- Clarified `GetTarget` wording so it does not imply a separate external scroll target; implementation returns the scroll bar's own `View` handle.
- Changed "bar width" to "bar's cross-axis thickness" because `SetBarThickness` applies to vertical and horizontal bars.
- Added the documented `Vector4` corner radius order for `SetBarCornerRadius(const Vector4&)`.
- Clarified that `UpdateScrollPosition` is the `IScrollBar` synchronization method and removed the redundant `SetScrollPosition` call from that example.
- Changed the `HideBar` description from "intended for" auto bars to "only affects" auto bars.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/scroll-bar.h` defines `ScrollBar::New`, uninitialized default construction, `GetTarget`, visibility setters/getters, `UpdateBarSize`, `UpdateScrollPosition`, `HideBar`, and typed style setters/getters.
- `repos/dali-ui/dali-ui-foundation/public-api/i-scroll-bar.h` documents `UpdateBarSize` parameter meanings and states that `HideBar` only affects bars with `Auto` visibility.
- `repos/dali-ui/dali-ui-foundation/integration-api/scroll-bar-impl.cpp` shows `GetTarget()` returning `View::DownCast(Self())`, `UpdateScrollPosition()` delegating to `SetScrollPosition()`, auto bars being shown during position updates, and fade-out applying only to auto bars.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-ScrollBar.cpp` verifies the public setters/getters and default values.

Remaining concerns:
- The guide intentionally keeps standalone `ScrollBar` examples, while the sample primarily demonstrates scroll bars through `Dali::Ui::ScrollView` and leaves standalone `ScrollBar` usage commented out.
- No build verification was run; this review was source-grounded against headers, implementation, samples, and UTCs.
