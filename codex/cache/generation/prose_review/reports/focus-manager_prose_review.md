# Focus Manager Prose Review

Changed prose:
- Clarified `SetCurrentFocusView()` wording to say it sets focus without child delegation and requires the exact view to be focusable and on the scene.
- Added that `RequestFocus()` can be rejected when an ancestor blocks descendant focus.
- Narrowed focus group wording from all focus movement to `MoveFocus()` navigation, because UTC coverage shows direct `RequestFocus()` outside the group can still succeed.
- Changed the focus indicator paragraph from “owns” to “maintains” to avoid implying ownership semantics not exposed by the public API.
- Clarified that `MoveFocusBackward()` returns to a previously focused view recorded in focus history.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/focus-manager/focus-manager.h` documents `SetCurrentFocusView()`, `RequestFocus()`, focus groups, focus indicators, focus change signals, device inspection, and default window focus clearing.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-FocusManager.cpp` verifies `RequestFocus()` child delegation, `SetCurrentFocusView()` no-delegation behavior, focus group containment for `MoveFocus()`, and direct `RequestFocus()` outside a group.
- `repos/dali-ui/dali-ui-foundation/internal/focus-manager/focus-manager-impl.cpp` confirms default `mClearFocusOnWindowFocusLost(true)`, focus history behavior for `MoveFocusBackward()`, and focus indicator replacement behavior.

Remaining concerns:
- The guide keeps the existing code-block style and examples as requested. Some snippets assume the passed `Dali::Ui::View` handles are already initialized, focusable where needed, and on scene.
