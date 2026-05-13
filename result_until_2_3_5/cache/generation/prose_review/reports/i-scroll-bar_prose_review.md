# I Scroll Bar Prose Review

Changed prose:
- Adjusted the overview from “keeping a scroll bar synchronized with a `Dali::Ui::View` scroll target” to “updating scroll-bar visibility, size, and position for a `Dali::Ui::View` target” to match the actual `IScrollBar` method surface without over-describing ownership.
- Changed “Application code normally receives an object” to “Application code can work with an object” because the public API also exposes concrete `Dali::Ui::ScrollBar::New()`.
- Clarified `GetTarget()` as returning the `Dali::Ui::View` target associated with the implementation, rather than saying the bar “represents” or “tracks” a scrollable view.
- Added the public `Dali::Ui::ScrollBarVisibility` values: `Auto`, `Always`, and `Never`.
- Tightened `HideBar()` prose to say it affects `Dali::Ui::ScrollBarVisibility::Auto` bars specifically.
- Changed “layout changes” to “layout or content-size changes” for the typical update path.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/i-scroll-bar.h` defines `Dali::Ui::IScrollBar`, `GetTarget()`, axis visibility getters/setters, `UpdateBarSize()`, `UpdateScrollPosition()`, and `HideBar()`.
- `repos/dali-ui/dali-ui-foundation/public-api/scrollable-enum.h` defines `ScrollBarVisibility::Auto`, `Always`, and `Never`.
- `repos/dali-ui/dali-ui-foundation/public-api/scroll-bar.h` shows `Dali::Ui::ScrollBar` implements `Dali::Ui::IScrollBar`.
- `repos/dali-ui/dali-ui-foundation/integration-api/scroll-bar-impl.cpp` stores visibility per axis, removes bars for `Never`, shows bars for `Always`, updates size from scrollable and viewport dimensions, updates position through `SetScrollPosition()`, and fades out `Auto` bars in `PlayFadeOut()`.
- `repos/dali-ui/dali-ui-foundation/integration-api/scroll-view-impl.cpp` updates visibility, bar size, and scroll position together when scrolling properties change.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-ScrollBar.cpp` verifies default `Auto` visibility, visibility setters/getters, and non-crashing calls for `UpdateBarSize()` and `UpdateScrollPosition()`.

Remaining concerns:
- The guide keeps the original code blocks as requested. They are API-shape examples rather than complete runnable app snippets.
- `GetTarget()` behavior is implementation-dependent; the concrete `ScrollBarImpl` currently returns `View::DownCast(Self())`, so the guide avoids promising that it returns a separate scroll-content view.
