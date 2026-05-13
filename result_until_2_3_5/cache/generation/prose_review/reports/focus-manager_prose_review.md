# Focus Manager Prose Review

Changed prose:
- Clarified `RequestFocus` behavior: it resolves to an eligible descendant for containers, falls back to the supplied focusable view, and differs from exact `SetCurrentFocusView`.
- Adjusted the first `RequestFocus` example to return the request result instead of requiring `GetCurrentFocusView() == primaryView`, which is false when focus is delegated to a descendant.
- Narrowed focus group language to `MoveFocus` traversal containment and added that direct `RequestFocus` can still move focus outside the group.
- Reworded `GetFocusGroup` prose from "owns" to "contains" for closer alignment with closest-ancestor behavior.
- Changed window focus loss prose from "can clear" to "clears" by default.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/focus-manager/focus-manager.h` documents `RequestFocus`, `SetCurrentFocusView`, `SetAsFocusGroup`, `GetFocusGroup`, `FocusChangedSignal`, device inspection, and the default window-focus-loss policy.
- `repos/dali-ui/dali-ui-foundation/internal/focus-manager/focus-manager-impl.cpp` shows `RequestFocus` delegating through `ViewImpl::RequestFocus`, `SetCurrentFocusView` setting the supplied view directly, focus groups affecting traversal, `FocusChangedSignal` emission order, and `mClearFocusOnWindowFocusLost(true)`.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-FocusManager.cpp` verifies container delegation, direct focus behavior, focus group traversal containment, and that direct `RequestFocus` outside a group succeeds.

Remaining concerns:
- Examples intentionally omit full scene setup; the APIs require initialized views, focusable targets, and scene membership for successful focus changes.
- `SetFocusIndicatorActor` keeps its public API name even though the signature takes `Dali::Ui::View`; the guide preserves the API spelling.
