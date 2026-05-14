# I Scroll Bar Prose Review

Changed prose:
- Adjusted the overview from "controlling" to "displaying and updating" scroll bar behavior to match the public `IScrollBar` documentation, which describes the interface as responsible for displaying and controlling scrolling of a target view.
- Narrowed "concrete dali-ui component that owns or exposes a scroll bar" to "concrete dali-ui component that implements scroll bar behavior" because the public evidence for this feature is `Dali::Ui::ScrollBar : public AbsoluteLayout, public IScrollBar`.
- Aligned `ScrollBarVisibility` descriptions with `scrollable-enum.h`: `Auto` means show only when scrolling, `Always` means always show, and `Never` means never show.
- Clarified `GetTarget()` behavior. The interface returns a `Dali::Ui::View`, and the concrete `ScrollBarImpl::GetTarget()` returns `View::DownCast(Self())`, so the guide now states that `Dali::Ui::ScrollBar` returns its own view handle.
- Tightened `HideBar()` wording to "fade-out of auto-visibility scroll bars" because the public header says it only affects bars with `Auto` visibility, and the implementation fades out only bars whose visibility is `ScrollBarVisibility::Auto`.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/i-scroll-bar.h` defines `Dali::Ui::IScrollBar`, `GetTarget()`, visibility getters/setters, `UpdateBarSize()`, `UpdateScrollPosition()`, and `HideBar()`.
- `repos/dali-ui/dali-ui-foundation/public-api/scrollable-enum.h` defines `Dali::Ui::ScrollBarVisibility::{Auto, Always, Never}` and their intended meanings.
- `repos/dali-ui/dali-ui-foundation/public-api/scroll-bar.h` confirms `Dali::Ui::ScrollBar` is the concrete public class implementing `Dali::Ui::IScrollBar`.
- `repos/dali-ui/dali-ui-foundation/integration-api/scroll-bar-impl.cpp` confirms `GetTarget()` returns `View::DownCast(Self())`, `UpdateBarSize()` stores scrollable and viewport dimensions, `UpdateScrollPosition()` forwards to `SetScrollPosition()`, and `HideBar()` triggers fade-out behavior for `Auto` bars.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-ScrollBar.cpp` confirms default visibility is `Auto`, setters/getters round-trip visibility values, and `UpdateBarSize()` / `UpdateScrollPosition()` are callable public behavior.

Remaining concerns:
- The draft focuses on `Dali::Ui::IScrollBar` and avoids adding `Dali::Ui::ScrollView` examples, even though `ScrollView` exposes related app-facing visibility setters. This was left unchanged to preserve the feature target and section structure.
- The implementation comments around auto-hide timing are internally inconsistent with the timer value, so no timing duration was added to the guide.
