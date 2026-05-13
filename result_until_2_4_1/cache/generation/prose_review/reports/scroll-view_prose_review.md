# Scroll View Prose Review

Changed prose:
- Changed `SetScrollPosition()` description from setting the stored offset directly to applying a scroll position without animation. Source evidence: `ScrollViewImpl::SetScrollPosition()` delegates to `ScrollTo(position, false)`, and `ScrollTo()` adjusts the requested position before applying it.
- Added the `ScrollTo(View child, bool animation, ScrollToPosition)` overload to the position section. Source evidence: public `scroll-view.h` declares the child-view overload, and `scrollable-enum.h` defines `ScrollToPosition::{Start, Center, End, MakeVisible}`.
- Changed "motion policy" to "motion settings" for getter prose, because the public API exposes stored fling/over-scroll values rather than a separate policy object.
- Adjusted `OverScrollMode` prose to describe it as the public policy value and align wording with `scrollable-enum.h`.
- Changed move-assignment prose from "ownership of a handle" to "a handle value" to avoid implying unique ownership. Source evidence: `ScrollView` follows DALi handle semantics and has copy and move assignment.

Source evidence used:
- `repos/dali-ui/dali-ui-foundation/public-api/scroll-view.h`
- `repos/dali-ui/dali-ui-foundation/public-api/scroll-view.cpp`
- `repos/dali-ui/dali-ui-foundation/public-api/scrollable-enum.h`
- `repos/dali-ui/dali-ui-foundation/public-api/scroll-view.autogen.h`
- `repos/dali-ui/dali-ui-foundation/integration-api/scroll-view-impl.cpp`
- `repos/dali-ui/samples/scrollview/scrollview-example.cpp`
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-ScrollView.cpp`

Remaining concerns:
- `OverScrollMode` is publicly documented and stored by setter/getter, but the current implementation evidence found only stores `mOverScrollMode`; I did not find enforcement logic that changes bounds behavior based on the mode.
- Code blocks were preserved except for the localized addition of a child-view `ScrollTo()` example.
