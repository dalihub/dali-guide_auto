# Scroll Bar Prose Review

Changed prose:
- Clarified that `Dali::Ui::ScrollBar` is a dali-ui view type and that app code should prefer typed setters and `Dali::Ui::View` handles.
- Corrected `GetTarget` wording: implementation returns the scroll bar itself as a `Dali::Ui::View`.
- Tightened visibility wording to match `ScrollBarVisibility` comments and implementation behavior.
- Clarified `UpdateScrollPosition` versus `SetScrollPosition` without implying different stored state behavior.
- Replaced "bar width" with "cross-axis thickness" for `SetBarThickness`, because the same property applies to vertical and horizontal bars.
- Added the public `SetBarCornerRadius(const Dali::Vector4&)` overload to the prose without restructuring the section.
- Replaced ownership-style wording for handle transfer with copy/move handle semantics.
- Added a small safety note after `DownCast` because it can return an uninitialized handle.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/scroll-bar.h` declares `Dali::Ui::ScrollBar`, `New`, `DownCast`, `GetTarget`, visibility setters/getters, size/position update methods, styling setters/getters, and copy/move handle operations.
- `repos/dali-ui/dali-ui-foundation/public-api/i-scroll-bar.h` documents `UpdateBarSize`, `UpdateScrollPosition`, and `HideBar`; `HideBar` is described as fade-out behavior called when scrolling ends and only affecting `Auto` bars.
- `repos/dali-ui/dali-ui-foundation/public-api/scrollable-enum.h` defines `Auto`, `Always`, and `Never`.
- `repos/dali-ui/dali-ui-foundation/integration-api/scroll-bar-impl.cpp` shows `GetTarget()` returns `View::DownCast(Self())`, default visibility is `Auto`, `Never` removes bars, `Always` shows bars, `UpdateScrollPosition()` delegates to `SetScrollPosition()`, and `DownCast` may yield an uninitialized handle.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-ScrollBar.cpp` verifies creation, downcast success/failure, copy/move operations, default values, setters/getters, and no-crash update calls.
- `repos/dali-ui/samples/scrollview/scrollbar-example.cpp` shows application-facing usage with `Dali::Ui::ScrollView`, `Dali::Ui::ScrollBarVisibility`, and commented standalone `ScrollBar` configuration.

Remaining concerns:
- The standalone examples are API-accurate, but the main sample emphasizes configuring scroll bars through `Dali::Ui::ScrollView`; the guide may later add a short integration note if section restructuring becomes allowed.
- `UpdateBarSize` and `UpdateScrollPosition` behavior is verified mainly by implementation and no-crash UTCs; the public tests do not assert final thumb bounds.
