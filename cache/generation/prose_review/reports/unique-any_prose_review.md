# Unique Any Prose Review

## Summary

The draft is accurate and well-aligned with the public API. No prose changes were required.

## Verification Against Source Evidence

### API Signatures Verified (from `repos/dali-ui/dali-ui-foundation/public-api/unique-any.h`)

| API | Draft Description | Header Evidence | Status |
|-----|-------------------|-----------------|--------|
| `UniqueAny()` default constructor | "creates an empty UniqueAny that holds no value" | `UniqueAny();` creates empty | ✓ Accurate |
| `UniqueAny(T&& value)` | "value is moved into the internal storage" | Template constructor with `Dali::Forward<T>(value)` | ✓ Accurate |
| `Empty()` | "True if no value is stored" | `bool Empty() const;` returns true if no value | ✓ Accurate |
| `operator bool()` | "True if a value is stored" | `explicit operator bool() const;` | ✓ Accurate |
| `Get<T>()` | "returns nullptr if the type does not match" | Returns `nullptr` if `!IsType<ValueType>()` | ✓ Accurate |
| `Detach<T>()` | "On success, UniqueAny becomes empty; on type mismatch, unchanged" | Implementation shows early return on type mismatch, otherwise `mHolder = nullptr` | ✓ Accurate |
| `Reset()` | "clears the stored value" | `void Reset();` | ✓ Accurate |
| Move-only | "cannot be copied" | Copy constructor/assignment deleted | ✓ Accurate |

### Code Examples Verified (from `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-UniqueAny.cpp`)

| Example | UTC Evidence | Status |
|---------|--------------|--------|
| `Get<T>()` with type mismatch returning nullptr | `UtcDaliUniqueAnyGetP`: `DALI_TEST_CHECK(!attachment.Get<float>())` | ✓ Accurate |
| `Reset()` clearing value | `UtcDaliUniqueAnyResetP`: confirms `Empty()` and `!attachment` after reset | ✓ Accurate |
| `Detach<T>()` type mismatch behavior | `UtcDaliUniqueAnyDetachP`: `DALI_TEST_CHECK(!mismatch.Get())` and `DALI_TEST_CHECK(attachment.Get<MoveOnly>())` | ✓ Accurate |
| `Detach<T>()` success behavior | `UtcDaliUniqueAnyDetachP`: confirms ownership transfer and empty after | ✓ Accurate |
| Move-only type storage | `UtcDaliUniqueAnyMoveOnlyP`: confirms `MoveOnly` can be stored and retrieved | ✓ Accurate |

## Remaining Concerns

None. The draft accurately describes the `UniqueAny` API with correct code examples that match the public header and UTC tests.

## Changes Made

None required. The draft prose is accurate and the code blocks are verified against source evidence.
