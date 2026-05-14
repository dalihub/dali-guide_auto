---
title: Unique Any
sidebar_label: Unique Any
category: utilities
---

# Unique Any

`UniqueAny` provides type-erased storage for a single value, supporting both copyable and move-only types. It is used internally by dali-ui for view attachments and can store arbitrary values with type-safe retrieval.

## Table of Contents

- [Storing Values](#storing-values)
- [Retrieving Values](#retrieving-values)
- [Transferring Ownership](#transferring-ownership)
- [Checking and Clearing State](#checking-and-clearing-state)

## Storing Values

Construct a `UniqueAny` by passing a value to the constructor. The value is moved into the internal storage, allowing move-only types to be stored.

```cpp
// Store a primitive value
Dali::Ui::UniqueAny attachment(42);

// Store a move-only type
struct MoveOnly
{
  explicit MoveOnly(int v) : value(v) {}
  MoveOnly(MoveOnly&&) noexcept = default;
  MoveOnly(const MoveOnly&) = delete;
  int value;
};

Dali::Ui::UniqueAny moveAttachment(MoveOnly(7));
```

The default constructor creates an empty `UniqueAny` that holds no value:

```cpp
Dali::Ui::UniqueAny empty;  // Empty, holds no value
```

## Retrieving Values

Use the `Get<T>()` method to retrieve a pointer to the stored value. The method returns `nullptr` if the requested type does not match the stored type.

```cpp
Dali::Ui::UniqueAny attachment(3);

int* value = attachment.Get<int>();
if (value)
{
  // Use *value
}

// Returns nullptr for type mismatch
float* wrong = attachment.Get<float>();  // nullptr
```

The returned pointer is owned by the `UniqueAny`. Do not delete it or use it after the `UniqueAny` is destroyed or moved from.

## Transferring Ownership

Use `Detach<T>()` to transfer ownership of the stored value out of the `UniqueAny`. On success, the `UniqueAny` becomes empty and ownership is returned in a `Dali::UniquePtr`.

```cpp
Dali::Ui::UniqueAny attachment(MoveOnly(17));

// Type mismatch leaves UniqueAny unchanged
Dali::UniquePtr<int> mismatch = attachment.Detach<int>();
// mismatch.Get() == nullptr, attachment still holds MoveOnly

// Correct type transfers ownership
Dali::UniquePtr<MoveOnly> value = attachment.Detach<MoveOnly>();
if (value.Get())
{
  // Ownership transferred, use value->value
}

// attachment is now empty
```

## Checking and Clearing State

Check whether a `UniqueAny` holds a value using `Empty()` or the boolean conversion operator:

```cpp
Dali::Ui::UniqueAny attachment(3);

if (attachment)  // True if holds a value
{
  // attachment stores something
}

if (!attachment.Empty())  // Same check
{
  // attachment stores something
}
```

Call `Reset()` to clear the stored value:

```cpp
Dali::Ui::UniqueAny attachment(3);
attachment.Reset();  // Now empty

// After Reset:
// attachment.Empty() == true
// attachment.Get<int>() == nullptr
```

`UniqueAny` is move-only and cannot be copied:

```cpp
Dali::Ui::UniqueAny a(5);
Dali::Ui::UniqueAny b(std::move(a));  // OK: move constructor
a = std::move(b);                      // OK: move assignment
// Dali::Ui::UniqueAny c(a);          // Error: copy constructor deleted
```