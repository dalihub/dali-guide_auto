# Scroll View Prose Review

Changed prose:
- Reworded the opening sentence from "panning content" to "scrolling content" to match the public `ScrollView` description while keeping the guide flow.
- Tightened `OverScrollMode::ContentScrolls` wording to "only when the content is scrollable", matching `scrollable-enum.h`.
- Changed "owns scroll bar visibility settings directly" to "exposes scroll bar visibility settings directly" to avoid overclaiming implementation ownership while preserving the guidance.
- Added that scroll position APIs should be used after `SetContent`, based on implementation behavior and existing repository documentation.
- Added the `ScrollTo(View child, bool, ScrollToPosition)` overload in the existing scroll position section because it is a public `ScrollView` API and fits the section without restructuring.
- Replaced the `GetPositionIfIdle` example, which returned the same value in both branches, with a focused `IsScrollInProgress` example.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/scroll-view.h` defines `Dali::Ui::ScrollView` as a `View`, `SetContent(View)`, `GetContent()`, scroll direction, fling setters/getters, scroll bar visibility setters/getters, `IsScrolling()`, `ScrollTo(Vector2)`, `ScrollTo(View child, ...)`, `ScrollToX`, `ScrollToY`, and all scroll/drag signal signatures.
- `repos/dali-ui/dali-ui-foundation/public-api/scrollable-enum.h` defines `ScrollDirection`, `ScrollBarVisibility`, `ScrollToPosition`, and `OverScrollMode` meanings.
- `repos/dali-ui/dali-ui-foundation/public-api/scroll-view.autogen.h` confirms the chainable `DALI_UI_CHAIN_SCROLLVIEW_METHODS` setters.
- `repos/dali-ui/samples/scrollview/scrollview-example.cpp` and `repos/dali-ui/samples/scrollview/scrollbar-example.cpp` show app-facing `ScrollView::New()`, fluent setter chaining, `SetContent`, fling tuning, over-scroll mode, and scroll bar visibility usage.
- `repos/dali-ui/samples/scrollview/scrollview-interactive-children.cpp` shows scroll lifecycle signals and `GetScrollPosition()` callbacks in application-style code.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-ScrollView.cpp` verifies setter/getter behavior, chain returns, signal connection signatures, and `SetScrollPosition`.

Remaining concerns:
- The guide keeps the custom subclass macro example because it is sourced from the generated public header, but most application developers should not need it unless they are extending the handle pattern.
- Numeric fling examples are preserved from samples; the public header names them as duration and distance values but does not document units.
