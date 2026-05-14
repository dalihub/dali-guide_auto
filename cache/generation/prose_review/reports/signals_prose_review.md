# Signals Prose Review

## Summary

Reviewed the Signals guide draft against public headers (`dali-signal.h`, `connection-tracker.h`, `base-signal.h`, `slot-delegate.h`), sample code (`web-view-example.cpp`), and UTC tests (`utc-Dali-SignalTemplates.cpp`). Made targeted corrections to improve accuracy.

## Changes Made

### 1. EmitOr Section (Accuracy Fix)

**Original prose:**
> "For boolean-returning signals with multiple callbacks, `EmitOr()` returns `true` if any callback returns `true`"

**Revised prose:**
> "For signals with two parameters and a return value, `EmitOr()` aggregates results using the `|=` operator. For boolean-returning signals, this returns `true` if any callback returns `true`"
> "Note: `EmitOr()` is only available for `Signal<Ret(Arg0, Arg1)>` — signals with exactly two parameters and a return value."

**Source evidence:** `dali-signal.h` lines 580-603 shows `EmitOr()` is only defined for `Signal<Ret(Arg0, Arg1)>`. The `base-signal.h` line 197 shows the implementation uses `returnVal |= CallbackBase::ExecuteReturn<Ret, Args...>(*callback, args...);` which works for any type supporting `|=`.

### 2. SlotDelegate Section (Code Block Fix)

**Original code:**
```cpp
class MyComponent
{
public:
  void ConnectSignals(Signal<void()>& signal)
  {
    signal.Connect(mSlotDelegate, &MyComponent::OnEvent);
  }
  ...
private:
  SlotDelegate<MyComponent> mSlotDelegate;
};
```

**Revised code:**
```cpp
class MyComponent
{
public:
  MyComponent()
  : mSlotDelegate(this)
  {
  }

  void ConnectSignals(Signal<void()>& signal)
  {
    signal.Connect(mSlotDelegate, &MyComponent::OnEvent);
  }
  ...
private:
  SlotDelegate<MyComponent> mSlotDelegate;
};
```

**Source evidence:** `slot-delegate.h` line 68 shows the constructor signature `SlotDelegate(Slot* slot)`. The header's example code at lines 38-66 demonstrates proper initialization with `mSlotDelegate(this)` in the constructor.

### 3. Minor Variable Name Consistency

Changed `Handler1`, `Handler2`, `Handler3` to `handler1`, `handler2`, `handler3` in the Multiple Connections section to follow consistent naming conventions (lowercase for instances).

## Verified Accurate (No Changes Needed)

- **Overview paragraph**: Accurately describes signals as type-safe publish-subscribe with automatic disconnection.
- **ConnectionTracker inheritance pattern**: Correctly shows `ConnectionTracker` base class and automatic disconnection behavior.
- **Static function connection**: Correct syntax for connecting/disconnecting static functions.
- **View signal connection**: Matches sample code patterns in `web-view-example.cpp`.
- **Emit() behavior**: Correctly describes return value as "last callback's return value" per `dali-signal.h` documentation.
- **Signal variants (0-3 parameters)**: All template specializations verified against `dali-signal.h`.
- **Connection during emission behavior**: Verified by `UtcDaliSignalConnectDuringEmitNotFired` test.
- **Disconnection during emission**: Verified by `UtcDaliSignalDisconnectDuringCallback` tests.

## Remaining Considerations

None. All identified issues have been addressed with minimal, targeted edits.
