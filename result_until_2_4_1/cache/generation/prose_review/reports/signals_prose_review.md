# Signals Prose Review

Changed prose:
- Clarified the overview so `Dali::ConnectionTracker` is described as the cleanup owner rather than implying cleanup is inherent to every `Dali::Signal` connection.
- Narrowed the signature statement to member-function connections, because `Dali::Signal` also supports function-object overloads.
- Changed "`Dali::ConnectionTracker` is the preferred owner" to "usual owner" to avoid overclaiming beyond the public header guidance.
- Refined `EmitOr` wording: implementation uses `|=` in `BaseSignal::EmitReturnOr`; for `bool` this produces logical OR style behavior.
- Changed the recursive emit sentence from "guard against" to "detect nested emission" because the public `EmitGuard` reports an error condition and the emit helpers return a default result.
- Updated code snippets using `Dali::Ui::View` by including `<dali-ui-foundation/public-api/view.h>` and removing incomplete forward declarations.

Source evidence:
- `repos/dali-core/dali/public-api/signals/dali-signal.h`: typed `Dali::Signal` specializations, `Connect` overloads for member functions, `SlotDelegate`, function objects, `FunctorDelegate`, `Emit`, `EmitOr`, `Empty`, and `GetConnectionCount`.
- `repos/dali-core/dali/public-api/signals/connection-tracker.h`: `Dali::ConnectionTracker`, `DisconnectAll`, and `GetConnectionCount`.
- `repos/dali-core/dali/public-api/signals/slot-delegate.h`: `Dali::SlotDelegate` owns a `ConnectionTracker`, supports `DisconnectAll`, `GetConnectionCount`, `GetSlot`, and `GetConnectionTracker`.
- `repos/dali-core/dali/public-api/signals/base-signal.h`: `Dali::BaseSignal`, `EmitGuard`, `Empty`, `GetConnectionCount`, `EmitReturn`, and `EmitReturnOr`.
- `repos/dali-core/dali/public-api/signals/functor-delegate.h`: `Dali::FunctorDelegate::New` allocates and transfers ownership of a functor delegate.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`: `Dali::Ui::View` is a concrete public handle type, so snippets storing it by value need the full header.
- `repos/dali-ui/samples/color-controls/color-controls-example.cpp`: sample evidence for dali-ui trait signal lambda connections with `View` callback parameters.
- `repos/dali-core/automated-tests/src/dali/utc-Dali-SignalTemplates.cpp`: UTC evidence for `GetConnectionCount`, `SlotDelegate::DisconnectAll`, `FunctorDelegate::New`, and `EmitOr` behavior.

Remaining concerns:
- The examples are intentionally compact and model application-level signal patterns. They were not compiled as standalone translation units in this read-only review environment.
- The guide targets public `Dali::Signal` mechanics used by dali-ui applications; it does not enumerate every overload or internal callback class.
