---
title: Signals
sidebar_label: Signals
category: utilities
---

# Signals

`Dali::Signal` connects DALi events to application callbacks, and `Dali::ConnectionTracker` lets those connections be cleaned up when the callback owner is destroyed.

## Table of Contents

- [Connect Signals from a dali-ui Controller](#connect-signals-from-a-dali-ui-controller)
- [Use Typed Signal Signatures](#use-typed-signal-signatures)
- [Manage Lifetime and Disconnection](#manage-lifetime-and-disconnection)
- [Connect Lambdas and Function Objects](#connect-lambdas-and-function-objects)
- [Emit Application-Level Signals](#emit-application-level-signals)
- [Inspect Signal State](#inspect-signal-state)

## Connect Signals from a dali-ui Controller

In a dali-ui application, keep signal callbacks on an object that owns the UI logic. Inherit from `Dali::ConnectionTracker` when the controller should automatically disconnect its tracked signal callbacks during destruction.

The common connection form is:

```cpp
signal.Connect(this, &Controller::OnSignal);
```

For member-function connections, the callback signature must match the signal signature.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/signals/connection-tracker.h>
#include <dali/public-api/signals/dali-signal.h>

class DetailsController : public Dali::ConnectionTracker
{
public:
  using ActivatedSignalType = Dali::Signal<void(Dali::Ui::View)>;

  void ConnectTo(ActivatedSignalType& activatedSignal)
  {
    activatedSignal.Connect(this, &DetailsController::OnActivated);
  }

private:
  void OnActivated(Dali::Ui::View view)
  {
    mLastActivatedView = view;
  }

  Dali::Ui::View mLastActivatedView;
};
```

`Dali::ConnectionTracker` is the usual owner for application callbacks because it tracks connected slots and disconnects them when the tracker goes away.

## Use Typed Signal Signatures

`Dali::Signal` is specialized by function signature. Choose a signature that describes exactly what the callback receives and returns.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/signals/dali-signal.h>

using ReadySignalType      = Dali::Signal<void()>;
using SelectedSignalType   = Dali::Signal<void(Dali::Ui::View)>;
using ChangedSignalType    = Dali::Signal<void(Dali::Ui::View, int)>;
using InterceptSignalType  = Dali::Signal<bool(Dali::Ui::View, int)>;
using CompletedSignalType  = Dali::Signal<bool()>;
```

A `void` signal calls each connected callback without producing a result.

```cpp
class SelectionModel
{
public:
  using SelectedSignalType = Dali::Signal<void(Dali::Ui::View)>;

  SelectedSignalType& SelectedSignal()
  {
    return mSelectedSignal;
  }

  void Select(Dali::Ui::View view)
  {
    SelectedSignal().Emit(view);
  }

private:
  SelectedSignalType mSelectedSignal;
};
```

A non-`void` signal returns the value from the last callback invoked, or a default-constructed value when no callbacks are connected.

```cpp
class NavigationModel
{
public:
  using CanLeaveSignalType = Dali::Signal<bool(Dali::Ui::View)>;

  CanLeaveSignalType& CanLeaveSignal()
  {
    return mCanLeaveSignal;
  }

  bool CanLeave(Dali::Ui::View view)
  {
    return CanLeaveSignal().Emit(view);
  }

private:
  CanLeaveSignalType mCanLeaveSignal;
};
```

For two-argument return signals, `EmitOr` combines callback results with bitwise OR assignment. For `bool` signals, this gives event-style logical OR behavior where any callback can consume the event.

```cpp
class InputRouter
{
public:
  using InputSignalType = Dali::Signal<bool(Dali::Ui::View, int)>;

  InputSignalType& InputSignal()
  {
    return mInputSignal;
  }

  bool RouteInput(Dali::Ui::View view, int inputCode)
  {
    return InputSignal().EmitOr(view, inputCode);
  }

private:
  InputSignalType mInputSignal;
};
```

## Manage Lifetime and Disconnection

Use `Dali::ConnectionTracker::DisconnectAll` when a controller should stop receiving callbacks before its destructor runs.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/signals/connection-tracker.h>
#include <dali/public-api/signals/dali-signal.h>

class PageController : public Dali::ConnectionTracker
{
public:
  using ClosedSignalType = Dali::Signal<void(Dali::Ui::View)>;

  void Attach(ClosedSignalType& closedSignal)
  {
    closedSignal.Connect(this, &PageController::OnClosed);
  }

  void Detach()
  {
    DisconnectAll();
  }

private:
  void OnClosed(Dali::Ui::View view)
  {
    mClosedView = view;
  }

  Dali::Ui::View mClosedView;
};
```

If a class cannot inherit from `Dali::ConnectionTracker`, use `Dali::SlotDelegate`. The object that provides the callback stores the `Dali::SlotDelegate`, and the delegate owns an internal connection tracker.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/signals/dali-signal.h>
#include <dali/public-api/signals/slot-delegate.h>

class PlainPageController
{
public:
  using ClosedSignalType = Dali::Signal<void(Dali::Ui::View)>;

  PlainPageController()
  : mSlotDelegate(this)
  {
  }

  void Attach(ClosedSignalType& closedSignal)
  {
    closedSignal.Connect(mSlotDelegate, &PlainPageController::OnClosed);
  }

  void Detach()
  {
    mSlotDelegate.DisconnectAll();
  }

private:
  void OnClosed(Dali::Ui::View view)
  {
    mClosedView = view;
  }

  Dali::SlotDelegate<PlainPageController> mSlotDelegate;
  Dali::Ui::View                          mClosedView;
};
```

Use `Dali::SlotDelegate::GetConnectionCount` or `Dali::ConnectionTracker::GetConnectionCount` when a controller needs to expose diagnostics for active connections.

## Connect Lambdas and Function Objects

`Dali::Signal` can connect a function object with a `Dali::ConnectionTrackerInterface*`. This is useful for concise dali-ui view callbacks where the controller still owns the connection lifetime.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/signals/connection-tracker.h>
#include <dali/public-api/signals/dali-signal.h>

class ItemController : public Dali::ConnectionTracker
{
public:
  using PressedSignalType = Dali::Signal<void(Dali::Ui::View, bool)>;

  void Attach(PressedSignalType& pressedSignal)
  {
    pressedSignal.Connect(
      this,
      [this](Dali::Ui::View view, bool pressed)
      {
        OnPressed(view, pressed);
      });
  }

private:
  void OnPressed(Dali::Ui::View view, bool pressed)
  {
    mPressedView = view;
    mPressed     = pressed;
  }

  Dali::Ui::View mPressedView;
  bool           mPressed{false};
};
```

`Dali::FunctorDelegate::New` creates a delegate object for function-object connections. The signal connection takes ownership of the newly allocated `Dali::FunctorDelegate`.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali/public-api/signals/dali-signal.h>
#include <dali/public-api/signals/functor-delegate.h>

class RefreshController : public Dali::ConnectionTracker
{
public:
  using RefreshSignalType = Dali::Signal<void()>;

  void Attach(RefreshSignalType& refreshSignal)
  {
    refreshSignal.Connect(
      this,
      Dali::FunctorDelegate::New(
        [this]()
        {
          Refresh();
        }));
  }

private:
  void Refresh()
  {
    ++mRefreshCount;
  }

  int mRefreshCount{0};
};
```

## Emit Application-Level Signals

Application code can define its own `Dali::Signal` members to publish state changes from a reusable dali-ui component model. Expose the signal by reference and emit it from the owning class.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/signals/dali-signal.h>

class SelectionState
{
public:
  using SelectionChangedSignalType = Dali::Signal<void(Dali::Ui::View, int)>;

  SelectionChangedSignalType& SelectionChangedSignal()
  {
    return mSelectionChangedSignal;
  }

  void SetSelection(Dali::Ui::View selectedView, int selectedIndex)
  {
    mSelectedView  = selectedView;
    mSelectedIndex = selectedIndex;

    SelectionChangedSignal().Emit(mSelectedView, mSelectedIndex);
  }

private:
  SelectionChangedSignalType mSelectionChangedSignal;
  Dali::Ui::View             mSelectedView;
  int                        mSelectedIndex{0};
};
```

Avoid emitting the same `Dali::Signal` recursively. The signal implementation uses `Dali::BaseSignal::EmitGuard` to detect nested emission of the same signal.

## Inspect Signal State

Every typed `Dali::Signal` inherits the state queries provided by its signal mixin. Use `Empty` to check whether there are connected callbacks, and `GetConnectionCount` for diagnostics or test assertions.

```cpp
#include <cstdint>
#include <dali/public-api/signals/dali-signal.h>

class SaveModel
{
public:
  using SavedSignalType = Dali::Signal<void()>;

  SavedSignalType& SavedSignal()
  {
    return mSavedSignal;
  }

  const SavedSignalType& SavedSignal() const
  {
    return mSavedSignal;
  }

  bool HasSaveObservers() const
  {
    return !SavedSignal().Empty();
  }

  uint32_t GetSaveObserverCount() const
  {
    return SavedSignal().GetConnectionCount();
  }

  void Save()
  {
    if(!SavedSignal().Empty())
    {
      SavedSignal().Emit();
    }
  }

private:
  SavedSignalType mSavedSignal;
};
```

`Dali::BaseSignal` also provides `Empty`, `GetConnectionCount`, and the low-level emit helpers used by `Dali::Signal`. Application code should normally work through the typed `Dali::Signal<...>` interface exposed by the dali-ui object or by your own component model.
