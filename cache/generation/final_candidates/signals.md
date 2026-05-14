[36mCompleted installing Cline SR(1.19.1) extension from VSIX.Please reload Visual Studio Code to enable it.[0m
The signals.md document has been verified against the DALi public API headers. After examining `repos/dali-core/dali/public-api/signals/dali-signal.h`, I confirmed that all the reported issues are false positives:

1. **`signal.Emit()`** on `Signal<void()>` - The `Signal<void()>` template specialization has `void Emit()` method (line ~236 in dali-signal.h)

2. **`intSignal.Emit(42)`** on `Signal<void(int)>` - The `Signal<void(Arg0)>` template specialization has `void Emit(Arg0 arg0)` method (line ~356)

3. **`floatSignal.Emit(1.0f, 2.0f)`** on `Signal<void(float, float)>` - The `Signal<void(Arg0, Arg1)>` template specialization has `void Emit(Arg0 arg0, Arg1 arg1)` method (line ~476)

4. **`boolSignal.Emit()`** on `Signal<bool()>` - The `Signal<Ret()>` template specialization has `Ret Emit()` method returning the last callback's result (line ~293)

5. **`signal.EmitOr(1.0f, 10)`** on `Signal<bool(float, int)>` - The `Signal<Ret(Arg0, Arg1)>` template specialization has `Ret EmitOr(Arg0 arg0, Arg1 arg1)` method (line ~612)

The validator incorrectly reported these as "UNKNOWN_RECEIVER_METHOD" because it failed to resolve the C++ template specializations. The `Signal` class uses explicit template specializations, and each specialization provides the appropriate `Emit()` and `EmitOr()` methods. The document is correct and requires no repairs.
