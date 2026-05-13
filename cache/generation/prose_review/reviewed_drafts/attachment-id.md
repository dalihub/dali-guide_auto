---
title: Attachment Id
sidebar_label: Attachment Id
category: uncategorized
---

# Attachment Id

`AttachmentId` is a lightweight identifier for attaching custom data to `View` objects. Use attachments to store instance-specific state on Views without deriving custom handle classes.

## Table of Contents

- [Allocating an AttachmentId](#allocating-an-attachmentid)
- [Attaching Data to a View](#attaching-data-to-a-view)
- [Retrieving Attachment Data](#retrieving-attachment-data)
- [Detaching and Reattaching Data](#detaching-and-reattaching-data)
- [Using Attachments with Custom View Types](#using-attachments-with-custom-view-types)

## Allocating an AttachmentId

Allocate an `AttachmentId` once per logical attachment type using `AttachmentId::Alloc()`. Store the returned ID in static storage so it can be reused across `SetAttachment()`, `GetAttachment()`, and `DetachAttachment()` calls.

```cpp
// Anonymous namespace for file-scope attachment ID
namespace
{
const AttachmentId COUNTER_DATA_ID = AttachmentId::Alloc();
}
```

Do not allocate a new ID per object instance or per API call. Repeated one-shot allocation wastes the process-wide ID space and makes previously stored attachments impossible to retrieve.

For custom View types, encapsulate the ID allocation in a static member function:

```cpp
class MyButton : public InteractiveView
{
private:
  static AttachmentId GetDataId()
  {
    static AttachmentId id = AttachmentId::Alloc();
    return id;
  }
};
```

`AttachmentId::Alloc()` is thread-safe and lock-free. If the `uint32_t` ID space is exhausted, an assertion is raised instead of wrapping around.

## Attaching Data to a View

Use `View::SetAttachment()` to attach custom data to a View. The data ownership is transferred to the View via `Dali::UniquePtr<T>`.

```cpp
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
  .SetAttachment(COUNTER_DATA_ID, Dali::MakeUnique<CounterData>(label))
  .SetRequestedWidth(220.0f)
  .SetRequestedHeight(80.0f);
```

If another attachment already exists for the same ID, it is destroyed and replaced. The stored attachment is destroyed when it is removed, replaced, or when the View implementation is destroyed.

## Retrieving Attachment Data

Use `View::GetAttachment<T>()` to retrieve a pointer to the stored data. The returned pointer is owned by the View.

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

Always check the returned pointer for `nullptr` before use. A `nullptr` return indicates either that no attachment exists for the given ID or that the stored type does not match the requested type.

## Detaching and Reattaching Data

Use `View::DetachAttachment<T>()` to remove an attachment and take ownership of the data. This is useful for modifying and reattaching data without copying.

```cpp
void OnResetClicked(View view, InputEvent event)
{
  Dali::UniquePtr<CounterData> data = view.DetachAttachment<CounterData>(COUNTER_DATA_ID);
  if(data)
  {
    data->count = 0u;
    data->label.SetText("Count: 0");
    view.SetAttachment(COUNTER_DATA_ID, Dali::Move(data))
      .SetBackgroundColor(UiColor(0x1565C0));
  }
}
```

Use `View::RemoveAttachment()` to destroy an attachment without retrieving it:

```cpp
view.RemoveAttachment(COUNTER_DATA_ID);
```

## Using Attachments with Custom View Types

Attachments enable custom View types to store instance-specific state without adding fields to derived handle classes. DALi handle classes are lightweight wrappers over internal implementations; adding fields to derived handles causes state loss when the object is copied or upcast.

```cpp
class MyButton : public InteractiveView
{
public:
  struct Data
  {
    Data(Label label, UiColor normalColor, UiColor pressedColor)
    : label(label),
      normalColor(normalColor),
      pressedColor(pressedColor)
    {
    }

    Label   label;
    UiColor normalColor;
    UiColor pressedColor;
    uint32_t clickCount{0u};
  };

  static MyButton New(const Dali::String& text)
  {
    MyButton button(InteractiveView::New());
    button.Initialize(text);
    return button;
  }

  void Initialize(const Dali::String& text)
  {
    Label label = Label::New(text)
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT);

    Add(label);
    SetAttachment(GetDataId(), Dali::MakeUnique<Data>(label, UiColor(0x1565C0), UiColor(0x2E7D32)));
  }

  void IncrementClickCount()
  {
    Data* data = GetAttachment<Data>(GetDataId());
    if(data)
    {
      ++data->clickCount;
      SetBackgroundColor((data->clickCount % 2u) ? data->pressedColor : data->normalColor);
    }
  }

  static MyButton DownCast(BaseHandle handle)
  {
    InteractiveView view = InteractiveView::DownCast(handle);
    return view && view.GetAttachment<Data>(GetDataId()) ? MyButton(view) : MyButton();
  }

private:
  explicit MyButton(InteractiveView view)
  : InteractiveView(view)
  {
  }

  static AttachmentId GetDataId()
  {
    static AttachmentId id = AttachmentId::Alloc();
    return id;
  }
}
```

Avoid attaching data that strongly references the owner View, its parent, or an ancestor, as this can create reference cycles. Keeping a child handle is safe because DALi children do not strongly reference their parent.
