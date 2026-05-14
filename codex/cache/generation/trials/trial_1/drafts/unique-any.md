---
title: Unique Any
sidebar_label: Unique Any
category: utilities
---

# Unique Any

`Dali::Ui::UniqueAny` is a move-only, type-erased owner for storing one value, including move-only values, in dali-ui utility and `Dali::Ui::View` attachment workflows.

## Table of Contents

- [Store One Owned Value](#store-one-owned-value)
- [Read Values Safely by Type](#read-values-safely-by-type)
- [Clear or Move Stored Data](#clear-or-move-stored-data)
- [Detach Ownership](#detach-ownership)
- [Use Stable Attachment Keys](#use-stable-attachment-keys)
- [Internal Holder Model](#internal-holder-model)

## Store One Owned Value

Create a `Dali::Ui::UniqueAny` with the value you want to own. The constructor stores the decayed value type, so references and cv-qualifiers are not part of the stored type identity.

```cpp
#include <dali-ui-foundation/public-api/unique-any.h>

void StoreCounter()
{
  Dali::Ui::UniqueAny value(3);

  if(value)
  {
    const int* counter = value.Get<int>();
    if(counter)
    {
      // Use *counter while value is alive and still owns the stored int.
    }
  }
}
```

`Dali::Ui::UniqueAny::operator bool` returns `true` when a value is stored. `Dali::Ui::UniqueAny::Empty` returns `true` when no value is stored.

```cpp
#include <dali-ui-foundation/public-api/unique-any.h>

bool HasPayload(const Dali::Ui::UniqueAny& payload)
{
  return static_cast<bool>(payload) && !payload.Empty();
}
```

`Dali::Ui::UniqueAny` can also hold move-only application data.

```cpp
#include <dali-ui-foundation/public-api/unique-any.h>

struct PanelState
{
  explicit PanelState(unsigned int initialCount)
  : count(initialCount)
  {
  }

  PanelState(PanelState&& rhs) noexcept
  : count(rhs.count)
  {
    rhs.count = 0u;
  }

  PanelState& operator=(PanelState&& rhs) noexcept
  {
    count     = rhs.count;
    rhs.count = 0u;
    return *this;
  }

  PanelState(const PanelState&)            = delete;
  PanelState& operator=(const PanelState&) = delete;

  unsigned int count;
};

Dali::Ui::UniqueAny CreatePanelState()
{
  return Dali::Ui::UniqueAny(PanelState(1u));
}
```

## Read Values Safely by Type

Use `Dali::Ui::UniqueAny::Get` with the stored value type. The template argument is decayed before comparison, so references and cv-qualifiers are ignored. `Get` returns a pointer to the stored value when the type matches, or `nullptr` when the stored type is different.

```cpp
#include <dali-ui-foundation/public-api/unique-any.h>

void ReadCounter(const Dali::Ui::UniqueAny& payload)
{
  const int* counter = payload.Get<int>();
  if(counter)
  {
    // Type matched: *counter is the stored int.
  }

  const float* notCounter = payload.Get<float>();
  if(!notCounter)
  {
    // Type mismatch: payload is unchanged.
  }
}
```

The pointer returned by `Dali::Ui::UniqueAny::Get` is owned by the `Dali::Ui::UniqueAny`. Do not delete it, and do not keep it after the `Dali::Ui::UniqueAny` is reset, destroyed, detached, or moved from.

```cpp
#include <dali-ui-foundation/public-api/unique-any.h>

void UpdateState(Dali::Ui::UniqueAny& payload)
{
  PanelState* state = payload.Get<PanelState>();
  if(state)
  {
    ++state->count;
  }
}
```

## Clear or Move Stored Data

Call `Dali::Ui::UniqueAny::Reset` to destroy the stored value and make the object empty.

```cpp
#include <dali-ui-foundation/public-api/unique-any.h>

void ClearPayload()
{
  Dali::Ui::UniqueAny payload(3);

  payload.Reset();

  if(payload.Empty())
  {
    // The stored int has been destroyed.
  }
}
```

`Dali::Ui::UniqueAny` is move-only. Use the `Dali::Ui::UniqueAny` move constructor or move assignment when transferring the wrapper itself.

```cpp
#include <dali-ui-foundation/public-api/unique-any.h>
#include <utility>

Dali::Ui::UniqueAny BuildPayload()
{
  return Dali::Ui::UniqueAny(PanelState(10u));
}

void ReplacePayload()
{
  Dali::Ui::UniqueAny current(PanelState(1u));
  Dali::Ui::UniqueAny replacement(BuildPayload());

  current = std::move(replacement);

  PanelState* state = current.Get<PanelState>();
  if(state)
  {
    // current now owns the replacement state.
  }
}
```

After a move, use the destination object for the stored value. Treat pointers previously returned from the source object as invalid.

## Detach Ownership

Use `Dali::Ui::UniqueAny::Detach` when the caller should take ownership of the stored value. If the requested type matches, `Detach` returns a `Dali::UniquePtr<T>` and leaves the `Dali::Ui::UniqueAny` empty.

```cpp
#include <dali-ui-foundation/public-api/unique-any.h>

Dali::UniquePtr<PanelState> TakePanelState()
{
  Dali::Ui::UniqueAny payload(PanelState(17u));

  Dali::UniquePtr<PanelState> state = payload.Detach<PanelState>();
  if(state && payload.Empty())
  {
    // state owns the detached PanelState.
  }

  return state;
}
```

A type mismatch returns an empty `Dali::UniquePtr<T>` and leaves the original `Dali::Ui::UniqueAny` unchanged.

```cpp
#include <dali-ui-foundation/public-api/unique-any.h>

void TryWrongDetach()
{
  Dali::Ui::UniqueAny payload(PanelState(17u));

  Dali::UniquePtr<int> wrongType = payload.Detach<int>();
  if(!wrongType && payload.Get<PanelState>())
  {
    // The stored PanelState is still inside payload.
  }
}
```

## Use Stable Attachment Keys

`Dali::Ui::AttachmentId` identifies a logical attachment slot. Allocate one `Dali::Ui::AttachmentId` per attachment type and keep it in static storage so the same key can be reused by `Dali::Ui::View` attachment APIs such as `SetAttachment`, `GetAttachment`, and `DetachAttachment`.

```cpp
#include <dali-ui-foundation/public-api/attachment-id.h>

namespace
{
const Dali::Ui::AttachmentId PANEL_STATE_ATTACHMENT = Dali::Ui::AttachmentId::Alloc();
}

Dali::Ui::AttachmentId GetPanelStateAttachmentId()
{
  return PANEL_STATE_ATTACHMENT;
}
```

Do not allocate a new `Dali::Ui::AttachmentId` every time you access an attachment. A new id is a different key, so it will not find data stored under an earlier id.

```cpp
#include <dali-ui-foundation/public-api/attachment-id.h>

bool AttachmentIdsAreUnique()
{
  Dali::Ui::AttachmentId first  = Dali::Ui::AttachmentId::Alloc();
  Dali::Ui::AttachmentId second = Dali::Ui::AttachmentId::Alloc();

  return first != second;
}
```

For data that may be stored in one translation unit and retrieved in another, use stable namespace-scope value types. Avoid local or anonymous-namespace stored types for cross-translation-unit attachment data, because `Dali::Ui::UniqueAny::Get` and `Dali::Ui::UniqueAny::Detach` depend on the stored type identity matching exactly.

## Internal Holder Model

`Dali::Ui::UniqueAny::HolderBase` and `Dali::Ui::UniqueAny::Holder` are the internal type-erasure layer used by `Dali::Ui::UniqueAny`. Application code cannot access or construct them directly through the public API.

`Dali::Ui::UniqueAny::HolderBase::GetType` provides the stored type identity through the internal base interface, and `Dali::Ui::UniqueAny::Holder::GetType` returns the identity for the concrete stored type. `Dali::Ui::UniqueAny::Holder::mValue` is the owned value that `Dali::Ui::UniqueAny::Get` exposes by pointer and `Dali::Ui::UniqueAny::Detach` moves into a `Dali::UniquePtr<T>`.

Use the public `Dali::Ui::UniqueAny` operations instead:

```cpp
#include <dali-ui-foundation/public-api/unique-any.h>

void UsePublicUniqueAnyApi()
{
  Dali::Ui::UniqueAny payload(PanelState(5u));

  PanelState* state = payload.Get<PanelState>();
  if(state)
  {
    ++state->count;
  }

  Dali::UniquePtr<PanelState> detached = payload.Detach<PanelState>();
  if(detached)
  {
    // Ownership has moved out of payload.
  }
}
```
