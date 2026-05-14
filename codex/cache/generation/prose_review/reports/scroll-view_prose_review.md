# Scroll View Prose Review

Changed prose:
- Narrowed the overview from “displays a larger `Dali::Ui::View`” to “displays a `Dali::Ui::View`” because `SetContent` accepts any `View`; larger content is the common scrollable use case.
- Tightened `GetContent` wording from “inspect or replace content” to “inspect or replace the content” without changing meaning.
- Adjusted `Dali::Ui::OverScrollMode::ContentScrolls` wording to “only when the content is scrollable,” matching the enum comment.
- Clarified `ScrollToX` and `ScrollToY` behavior: implementation calls `ScrollTo(Vector2(position, mScrollPosition.y), ...)` or `ScrollTo(Vector2(mScrollPosition.x, position), ...)`, so the other coordinate is preserved from the current scroll position.
- Added a short localized note for the public child-view overload of `Dali::Ui::ScrollView::ScrollTo`, including `Dali::Ui::ScrollToPosition` values.
- Simplified the `CaptureStablePosition` code block because both branches returned `GetScrollPosition()`.
- Changed “create an empty handle with `Dali::Ui::ScrollView::ScrollView`” to “with the default `Dali::Ui::ScrollView` constructor,” which is more natural and avoids implying a static member call.
- Clarified that `DALI_UI_CHAIN_SCROLLVIEW_METHODS` is not normally used directly by application code.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/scroll-view.h` confirms `ScrollView`, `New`, `SetContent(View)`, `GetContent`, typed setters/getters, `ScrollTo`, `ScrollTo(View, bool, ScrollToPosition)`, `ScrollToX`, `ScrollToY`, `IsScrolling`, and signal callback signatures.
- `repos/dali-ui/dali-ui-foundation/public-api/scrollable-enum.h` confirms `ScrollDirection`, `ScrollBarVisibility`, `ScrollToPosition`, and `OverScrollMode` values and comments.
- `repos/dali-ui/dali-ui-foundation/integration-api/scroll-view-impl.cpp` confirms `ScrollToX` and `ScrollToY` preserve the other scroll-position coordinate.
- `repos/dali-ui/samples/scrollview/scrollbar-example.cpp` and `repos/dali-ui/samples/scrollview/scrollview-example.cpp` confirm fluent `ScrollView::New()` configuration with `SetContent`, scroll direction, fling tuning, overscroll, scroll bar visibility, and signal connections.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-ScrollView.cpp` confirms getter/setter behavior, `ScrollTo` position tests, signal connection signatures, and fluent setter return behavior.

Remaining concerns:
- The examples are API-oriented and do not show full sizing/layout setup for every snippet. That is acceptable for this localized prose review, and the samples provide complete application context.
