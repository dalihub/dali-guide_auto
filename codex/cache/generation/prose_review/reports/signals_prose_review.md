# Signals Prose Review

Changed prose:
- Tightened the overview to avoid implying that `Dali::ConnectionTracker` makes every connection inherently safe; the public API makes lifetime explicit through tracked connections.
- Replaced the broad "input-style signals that return `bool`" wording with the specific `Dali::Ui::View::KeyEventSignalType` behavior, because `Dali::Ui::InteractiveTrait::ClickedSignal()` is `Signal<void(View, InputEvent)>`.
- Clarified that focus callbacks receive `View, bool`, while view key callbacks receive `View, KeyEvent` and return `bool`.
- Adjusted the lambda callback example for `InteractiveTrait::ClickedSignal()` to use a `void` callback body instead of returning `bool`.
- Reworded `Dali::Signal<Ret(Arg0, Arg1)>` from "collects a return value" to "returns a value from connected callbacks."
- Reworded `EmitOr()` behavior to state that it returns true if any connected callback returns true.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` defines `View::KeyEventSignalType` as `Signal<bool(View, KeyEvent)>`, `View::FocusChangedSignalType` as `Signal<void(View, bool)>`, and `View::ResourceReadySignalType` as `Signal<void(View)>`.
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-trait.h` defines `ClickedSignal()` as `Signal<void(View, InputEvent)>`, `PressedChangedSignal()` as `Signal<void(View, bool, InputEvent)>`, and `LongPressedSignal()` as `Signal<bool(View, InputEvent)>`.
- `repos/dali-core/dali/public-api/signals/dali-signal.h` documents `SignalMixin::Empty()`, `SignalMixin::GetConnectionCount()`, `Connect()`, `Disconnect()`, `Emit()`, returning `Emit()`, and `EmitOr()`.
- `repos/dali-core/dali/public-api/signals/connection-tracker.h` documents `Dali::ConnectionTracker::DisconnectAll()` and `Dali::ConnectionTracker::GetConnectionCount()`.
- `repos/dali-core/automated-tests/src/dali/utc-Dali-SignalTemplates.cpp` verifies that `EmitOr()` stays true when an earlier callback returns true and a later callback returns false, while plain `Emit()` returns the last callback value.
- dali-ui samples such as `repos/dali-ui/samples/text/text-marquee-example.cpp`, `repos/dali-ui/samples/image-view/image-placeholder-example.cpp`, and `repos/dali-ui/samples/scrollview/scrollview-interactive-children.cpp` show application controllers inheriting `Dali::ConnectionTracker` and connecting view, image, key, focus, and trait signals.

Remaining concerns:
- The examples were preserved as much as possible and reviewed against public signatures, but they were not compiled in this read-only review pass.
- Some samples in the repository use lambdas returning `bool` with `ClickedSignal()` even though the public header documents `ClickedSignal()` as `Signal<void(View, InputEvent)>`; the revised guide follows the public header signature for app-facing guidance.
