# View State Prose Review

Changed prose:
- Revised the opening sentence from a visual/interaction-state description to a bitmask-focused description: `Dali::Ui::ViewState` represents one or more state bits for `Dali::Ui::View`.
- Replaced the generic callback paragraph with public API wording for `Dali::Ui::View::StateChangedSignal` and `Dali::Ui::StateEvent`.
- Replaced the signal example with a public `Dali::Ui::View::StateChangedSignal().Connect(...)` example using `Dali::ConnectionTracker`, `Dali::Ui::StateEvent::GetPrev`, and `Dali::Ui::StateEvent::GetCurrent`.
- Changed `Dali::Ui::ViewState::ALL` wording from “every registered state” to “every possible state bit,” matching its full-mask implementation.
- Clarified `Dali::Ui::ViewState::operator~` wording so it describes a complement mask over bits rather than a named-state registry.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/view-state.h` defines `Dali::Ui::ViewState` as a value type representing one or more view states as a bitmask, with `NORMAL`, `ALL`, predefined states, composite states, `Contains`, `HasIntersectionWith`, transition helpers, and bitwise operators.
- `repos/dali-ui/dali-ui-foundation/public-api/view-state.cpp` defines `Dali::Ui::ViewState::ALL` as `UINT32_MAX`, `NORMAL` as zero bits, predefined state names, and `ToString()` outputs `Normal`, `All`, or comma-separated registered names.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` exposes `Dali::Ui::View::StateChangedSignalType = Signal<void(View, StateEvent)>`, `GetState()`, and `StateChangedSignal()`.
- `repos/dali-ui/dali-ui-foundation/public-api/state-event.h` exposes `Dali::Ui::StateEvent::GetPrev`, `GetCurrent`, `Added`, `Removed`, and `Changed`.
- UTCs in `utc-Dali-ViewState2.cpp` verify default `NORMAL`, predefined/composite states, `Create`, `Contains`, `HasIntersectionWith`, operators, `ToString`, and transition helper behavior.
- UTCs in `utc-Dali-ViewState.cpp` and `utc-Dali-ViewStateHandler.cpp` verify `StateChangedSignal` and `ConnectionTracker` usage around `Dali::Ui::View` state transitions.

Remaining concerns:
- The guide intentionally avoids internal `IntegrationView::SetState` and `WhenStateChanged` APIs because the audience is application developers using public `dali-ui` APIs.
- The exact application-level APIs that set `FOCUSED`, `PRESSED`, `SELECTED`, and custom state bits are not fully documented in this draft; the guide focuses on interpreting and combining `Dali::Ui::ViewState` values.
