---
title: Attachment Id
sidebar_label: Attachment Id
category: views-components
---

# Attachment Id

`Dali::Ui::AttachmentId` is a lightweight identifier for attaching custom data to `View` instances. Use attachments to store per-object state without subclassing or adding fields to handle types.

## Table of Contents

- [Allocating an Attachment ID](#allocating-an-attachment-id)
- [Attaching Data to a View](#attaching-data-to-a-view)
- [Retrieving Attachment Data](#retrieving-attachment-data)
- [Detaching and Reattaching](#detaching-and-reattaching)
- [Comparison Operations](#comparison-operations)

## Allocating an Attachment ID

Allocate one `AttachmentId` per logical attachment type using `AttachmentId::Alloc()`. Store the returned ID in static storage so it can be reused across `SetAttachment()`, `GetAttachment()`, and `DetachAttachment()` calls.

```cpp
#include <dali-ui-foundation/public-api/attachment-id.h>

using namespace Dali::Ui;

// In an anonymous namespace or as a static member
namespace
{
const AttachmentId COUNTER_DATA_ID = AttachmentId::Alloc();
}
```

Do not allocate a new ID per object instance or per API call. Repeated one-shot allocation wastes the process-wide ID space and makes previously stored attachments impossible to retrieve.

`AttachmentId::Alloc()` is thread-safe and lock-free. It uses an internal atomic counter. If the `uint32_t` ID space is exhausted, an assertion is raised instead of wrapping around.

## Attaching Data to a View

Use `View::SetAttachment()` to attach custom data to a `View`. The data ownership is transferred to the View via `Dali::UniquePtr<T>`.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/object/unique-ptr.h>

using namespace Dali;
using namespace Dali::Ui;

struct CounterData
{
  explicit CounterData(Label label)
  : label(label)
  {
  }

  Label    label;
  uint32_t count{0u};
};

// Attach data to a View
InteractiveView counter = InteractiveView::New()
  .SetAttachment(COUNTER_DATA_ID, MakeUnique<CounterData>(label));
```

If another attachment already exists for the same ID, it is destroyed and replaced. The stored attachment is destroyed when removed, replaced, or when the View implementation is destroyed.

## Retrieving Attachment Data

Use `View::GetAttachment()` to retrieve a pointer to the stored data. The returned pointer is owned by the View.

```cpp
void UpdateLabel(View view)
{
  CounterData* data = view.GetAttachment<CounterData>(COUNTER_DATA_ID);
  if(data)
  {
    char text[64];
    std::snprintf(text, sizeof(text), "Count: %u", data->count);
    data->label.SetText(text);
  }
}
```

The pointer becomes invalid after the attachment is removed, replaced, or the View is destroyed. Always check for `nullptr` before using the returned pointer.

## Detaching and Reattaching

Use `View::DetachAttachment()` to remove an attachment and take ownership of the data. This is useful for modifying and reattaching data.

```cpp
void OnResetClicked(View view, AttachmentId id)
{
  UniquePtr<CounterData> data = view.DetachAttachment<CounterData>(id);
  if(data)
  {
    data->count = 0u;
    data->label.SetText("Count: 0");
    view.SetAttachment(id, Move(data));
  }
}
```

Use `View::RemoveAttachment()` to destroy an attachment without retrieving it:

```cpp
view.RemoveAttachment(COUNTER_DATA_ID);
```

## Comparison Operations

`AttachmentId` supports equality and inequality comparison via `operator==` and `operator!=`.

```cpp
AttachmentId id1 = AttachmentId::Alloc();
AttachmentId id2 = AttachmentId::Alloc();

if(id1 != id2)
{
  // Different attachment slots
}

if(id1 == id1)
{
  // Same attachment slot
}
```

The `value` member provides access to the underlying `uint32_t` identifier for debugging or logging purposes.