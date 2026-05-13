# Scroll Bar Prose Review

Changed prose:
- Reworded the overview from “for a scrollable `Dali::Ui::View`” to “for scrollable content in dali-ui applications” because `ScrollBar` can be used as a component and `ScrollView` owns an internal `ScrollBar`.
- Corrected `GetTarget()` wording. It returns the scroll bar handle downcast as `Dali::Ui::View`, not a separate represented target view.
- Added the documented default `SetBarMinSize()` and corner-radius values to the style paragraph.
- Added `SetBarCornerRadius(const Dali::Vector4&)` to the corner-radius paragraph because it is public API.
- Changed “content size” to “scrollable area” for `UpdateBarSize()` to match the public interface parameter names.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/scroll-bar.h` declares `ScrollBar::New()`, `DownCast()`, visibility setters/getters, `UpdateBarSize()`, `UpdateScrollPosition()`, `HideBar()`, style setters/getters, and both `SetBarCornerRadius()` overloads.
- `repos/dali-ui/dali-ui-foundation/integration-api/scroll-bar-impl.cpp` shows default values: thickness `4.0f`, color `Vector4(0.8f, 0.8f, 0.8f, 1.0f)`, min size `1.0f`, offset `2.0f`, corner radius `Vector4(1.0f, 1.0f, 1.0f, 1.0f)`, and default visibility `Auto`.
- `ScrollBarImpl::GetTarget()` returns `View::DownCast(Self())`.
- `ScrollBarImpl::SetScrollPosition()` stores `mScrollPosition` and calls `UpdatePosition()`. `UpdateScrollPosition()` delegates to `SetScrollPosition()`.
- `repos/dali-ui/dali-ui-foundation/public-api/scrollable-enum.h` documents `Auto`, `Always`, and `Never`.
- `repos/dali-ui/samples/scrollview/scrollbar-example.cpp` shows app-facing use with `ScrollView` visibility configuration and standalone `ScrollBar` setup comments.

Remaining concerns:
- The guide keeps standalone `Dali::Ui::ScrollBar` examples because the draft structure is focused on the `ScrollBar` class. For most application layouts, `Dali::Ui::ScrollView` visibility setters are the more common integration point.
