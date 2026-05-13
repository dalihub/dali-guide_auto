# Focus Manager Prose Review

Changed prose:
- Revised the focus group description from "limits focus navigation" to "contains focus movement" to match the public API wording and implementation behavior.
- Shortened `GetFocusGroup()` prose to "find the closest focus group for a view" because the implementation returns the view itself when it is a focus group, otherwise the closest ancestor focus group.
- Revised the focus group usage sentence from "until the application explicitly moves focus elsewhere" to "until the application explicitly requests focus elsewhere" because UTC coverage shows direct `RequestFocus()` outside a focus group succeeds while `MoveFocus()` remains contained.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/focus-manager/focus-manager.h`: confirms `FocusManager::Get()`, `RequestFocus()`, `SetCurrentFocusView()`, `MoveFocus()`, `MoveFocusBackward()`, focus groups, focus indicator, focus changed signal, clear-on-window-lost, and last focus-change device APIs.
- `repos/dali-ui/dali-ui-foundation/internal/focus-manager/focus-manager-impl.cpp`: confirms `RequestFocus()` delegates through `ViewImpl::RequestFocus()`, `SetCurrentFocusView()` sets exactly the provided focusable scene view, `MoveFocus()` resolves candidates through focus navigation and finder logic, `ClearFocus()` emits focus changed with an empty current view, and programmatic focus changes use `FocusDevice::PROGRAMMATIC`.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-FocusManager.cpp`: confirms `RequestFocus()` delegation to focusable descendants, `SetCurrentFocusView()` no-delegation behavior, focus group containment for `MoveFocus()`, direct `RequestFocus()` outside a focus group, and forward/backward navigation behavior.
- `repos/dali-ui/dali-ui-foundation/public-api/view-focus-enums.h`: confirms available `FocusDirection` and `FocusDevice` values, including `PROGRAMMATIC`.

Remaining concerns:
- The guide assumes example `Dali::Ui::View` handles are initialized, focusable where required, and on scene. That matches the focused guide scope, but a broader app guide could add a separate focus eligibility section.
- No code blocks were changed; they remain API-shape examples rather than complete runnable scenes.
