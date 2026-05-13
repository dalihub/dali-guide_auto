# Signals Prose Review

Changed prose:
- Replaced broad first-section examples with `Dali::Ui::InteractiveView`, `Dali::Ui::InteractiveTrait` attached to `Dali::Ui::View`, and `Dali::Ui::WebView` to avoid implying that every plain `Dali::Ui::View` exposes interaction signals directly.
- Clarified that `Dali::ConnectionTracker` disconnects connections made with that tracker, rather than all possible signal connections.
- Changed the `Dali::BaseSignal` paragraph to say typed `Dali::Signal` implementations call `Dali::BaseSignal::Emit()` and `Dali::BaseSignal::EmitReturn()`.
- Added a return-valued signal note that multiple callbacks use the last callback result.
- Corrected the functor paragraph to say the tracker is the first `Connect()` argument, not the first callback argument.
- Clarified nested emission behavior: a nested same-signal emission returns without invoking callbacks.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-trait.h` defines `ClickedSignal()` as `Signal<void(View, InputEvent)>`, `PressedChangedSignal()` as `Signal<void(View, bool, InputEvent)>`, and `LongPressedSignal()` as `Signal<bool(View, InputEvent)>`.
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-view.h` exposes the same interactive signals directly on `Dali::Ui::InteractiveView`.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` and `view.autogen.h` support `Dali::Ui::View::AsInteractive()` and fluent setters such as `SetRequestedWidth()`, `SetRequestedHeight()`, and `SetBackgroundColor()`.
- `repos/dali-ui/dali-ui-foundation/public-api/web-view.h` defines `PageLoadStartedSignalType` as `Signal<void(WebView, const Dali::String&)>` and `FrameRenderedSignalType` as `Signal<void(WebView)>`; `utc-Dali-WebView.cpp` connects callbacks with those signatures.
- `repos/dali-core/dali/public-api/signals/dali-signal.h` defines the typed `Dali::Signal` specializations, `Connect()` overloads, `Emit()`, `Empty()`, and `GetConnectionCount()`.
- `repos/dali-core/dali/public-api/signals/base-signal.h` and `base-signal.cpp` show `EmitGuard` returning early on nested emission, `EmitReturn()` returning the last callback value, and emission snapshot behavior for connections added during emission.
- `repos/dali-core/automated-tests/src/dali/utc-Dali-SignalTemplates.cpp` includes coverage for `Empty()`, `GetConnectionCount()`, functor connections, and callbacks connected during `Emit()` not firing until the next emission.
- `repos/dali-core/dali/public-api/signals/connection-tracker.cpp` shows `DisconnectAll()` and `GetConnectionCount()` behavior for tracked connections.

Remaining concerns:
- The draft examples were preserved as requested and not compiled in this review pass.
- The guide still uses custom application-level `Dali::Signal` examples for app-facing explanation; those are grounded in public `dali-core` signal APIs because the signal machinery itself is provided by `dali-core`.
