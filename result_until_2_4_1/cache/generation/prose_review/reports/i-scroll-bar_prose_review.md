# I Scroll Bar Prose Review

Changed prose:
- Clarified `GetTarget` wording so the guide says it returns the app-facing view exposed by the implementation, without over-specifying that it is the scrollable content view.
- Reworded the concrete `Dali::Ui::ScrollBar` guidance to allow application code to create or configure a concrete scroll bar view, while still recommending `Dali::Ui::IScrollBar` for reusable code.
- Changed “owns separate visibility settings” to “has separate visibility settings” for a more natural interface description.
- Tightened `ScrollBarVisibility::Auto` wording from “shows the bar during scrolling” to “shows the bar while scrolling.”
- Revised the vertical-list sizing note to avoid a speculative “enabled later” rationale and describe the actual width/viewport argument meaning.
- Changed “whenever the target view scroll position changes” to “when the target view scroll position changes” to avoid implying every implementation must call it on every frame.
- Clarified `HideBar` as an immediate option for manually controlled interactions, because the implementation also has auto-hide behavior for auto bars.
- Changed “typical scroll update path” to “typical manual scroll bar update path” so it does not imply `ScrollView` users must manually drive the internal scroll bar.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/i-scroll-bar.h` defines `Dali::Ui::IScrollBar`, `GetTarget`, visibility getters/setters, `UpdateBarSize`, `UpdateScrollPosition`, and `HideBar`.
- `repos/dali-ui/dali-ui-foundation/public-api/scroll-bar.h` shows `Dali::Ui::ScrollBar` as a public concrete type deriving from `AbsoluteLayout` and implementing `IScrollBar`, with `ScrollBar::New()`.
- `repos/dali-ui/dali-ui-foundation/public-api/scrollable-enum.h` defines `ScrollBarVisibility::Auto`, `Always`, and `Never`.
- `repos/dali-ui/dali-ui-foundation/integration-api/scroll-bar-impl.cpp` stores separate vertical and horizontal visibility, removes bars for `Never`, shows `Always`, updates thumb size from scrollable and viewport dimensions, updates position from `Vector2`, and fades out auto bars in `HideBar`.
- `repos/dali-ui/dali-ui-foundation/integration-api/scroll-view-impl.cpp` shows `ScrollView` owning an internal `ScrollBar`, applying visibility, size, and scroll position updates.
- `repos/dali-ui/samples/scrollview/scrollbar-example.cpp` shows app-facing use of `ScrollView` with `SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)` and `SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never)`.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-ScrollBar.cpp` verifies construction, visibility getters/setters, `UpdateBarSize`, and `UpdateScrollPosition`.

Remaining concerns:
- The public `IScrollBar::GetTarget` comment says “target view,” while `ScrollBarImpl::GetTarget()` currently returns `View::DownCast(Self())`. The guide avoids claiming this is the scrollable content view, but this API naming may still be ambiguous for application developers.
- The guide preserves the original manual-control examples. In normal app code, `Dali::Ui::ScrollView` appears to drive its internal scroll bar automatically.
