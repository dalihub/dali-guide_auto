---
title: Signals
sidebar_label: Signals
category: utilities
---

# Signals

Signals let a dali-ui application publish events and connect callbacks with automatic disconnection support.

## Table of Contents

- [Create View-Level Application Events](#create-view-level-application-events)
- [Connect Member Callbacks Safely](#connect-member-callbacks-safely)
- [Use Functors for Small Event Handlers](#use-functors-for-small-event-handlers)
- [Disconnect and Inspect Connections](#disconnect-and-inspect-connections)
- [Emit Return-Value Signals](#emit-return-value-signals)
- [Use `BaseSignal` Only Behind Typed Signals](#use-basesignal-only-behind-typed-signals)

## Create View-Level Application Events

In a dali-ui app, keep the app-facing object model around `Dali::Ui::View`. Use `Dali::Signal` for events that your view controller or component exposes to the rest of the application. The common forms are `Dali::Signal< void()>`, `Dali::Signal< void(Arg0)>`, `Dali::Signal< void(Arg0, Arg1)>`, and `Dali::Signal< void(Arg0, Arg1, Arg2)>`.

```cpp
#include <dali/public-api/signals/dali-signal.h>

class DocumentPanel
{
public:
  using ReadySignalType = Dali::Signal<void()>;
  using PageSignalType  = Dali::Signal<void(int)>;

  ReadySignalType& ReadySignal()
  {
    return mReadySignal;
  }

  PageSignalType& CurrentPageChangedSignal()
  {
    return mCurrentPageChangedSignal;
  }

  void NotifyReady()
  {
    static_cast<Dali::Signal<void()>&>(mReadySignal).Emit();
  }

  void SetCurrentPage(int pageIndex)
  {
    static_cast<Dali::Signal<void(int)>&>(mCurrentPageChangedSignal).Emit(pageIndex);
  }

private:
  ReadySignalType mReadySignal;
  PageSignalType  mCurrentPageChangedSignal;
};
```

`Dali::Signal` owns the connection list for one event. `Empty()` reports whether any callbacks are connected, and `GetConnectionCount()` returns the current number of connected callbacks.

```cpp
Dali::Signal<void()> readySignal;
Dali::Signal<void(int)> currentPageChangedSignal;

const bool noReadyHandlers = readySignal.Empty();
const auto pageHandlerCount = currentPageChangedSignal.GetConnectionCount();
```

## Connect Member Callbacks Safely

For member callbacks, derive the receiver from `Dali::ConnectionTracker`. When the receiver is destroyed, its tracked signal connections are disconnected automatically. This is the usual pattern for application controllers that own or coordinate `Dali::Ui::View` objects.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali/public-api/signals/dali-signal.h>

class DocumentController : public Dali::ConnectionTracker
{
public:
  void Bind(DocumentPanel& panel)
  {
    panel.ReadySignal().Connect(this, &DocumentController::OnPanelReady);
    panel.CurrentPageChangedSignal().Connect(this, &DocumentController::OnCurrentPageChanged);
  }

  void UnbindAll()
  {
    DisconnectAll();
  }

  uint32_t ActiveConnectionCount() const
  {
    return GetConnectionCount();
  }

private:
  void OnPanelReady()
  {
    mReady = true;
  }

  void OnCurrentPageChanged(int pageIndex)
  {
    mCurrentPage = pageIndex;
  }

private:
  bool mReady{false};
  int  mCurrentPage{0};
};
```

The member function signature must match the signal signature. A `Dali::Signal< void(int)>` connects to a callback shaped as `void Callback(int)`, while `Dali::Signal< void()>` connects to `void Callback()`.

## Use Functors for Small Event Handlers

`Dali::Signal` can connect a function object when you also pass a `Dali::ConnectionTrackerInterface`. For a controller that inherits `Dali::ConnectionTracker`, pass `this` as the tracker.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali/public-api/signals/dali-signal.h>

class PageCounter : public Dali::ConnectionTracker
{
public:
  void Bind(DocumentPanel& panel)
  {
    panel.CurrentPageChangedSignal().Connect(this, [this](int pageIndex)
    {
      mLastSeenPage = pageIndex;
      ++mChangeCount;
    });
  }

private:
  int mLastSeenPage{0};
  int mChangeCount{0};
};
```

When connecting through a delegate object, allocate it with `Dali::FunctorDelegate::New`. The signal connection takes ownership of the `Dali::FunctorDelegate`.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali/public-api/signals/dali-signal.h>
#include <dali/public-api/signals/functor-delegate.h>

class SaveNotifier : public Dali::ConnectionTracker
{
public:
  void Bind(DocumentPanel& panel)
  {
    panel.ReadySignal().Connect(this, Dali::FunctorDelegate::New([this]()
    {
      mCanSave = true;
    }));
  }

private:
  bool mCanSave{false};
};
```

Use this for concise handlers that belong to the receiver lifetime. For reusable behavior, a named member callback is usually clearer.

## Disconnect and Inspect Connections

Use `Disconnect()` when you need to remove one callback from one signal. Use `Dali::ConnectionTracker::DisconnectAll()` when the receiver should stop listening to every signal it has tracked.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali/public-api/signals/dali-signal.h>

class PageBadge : public Dali::ConnectionTracker
{
public:
  void Attach(DocumentPanel& panel)
  {
    mPanel = &panel;
    panel.CurrentPageChangedSignal().Connect(this, &PageBadge::OnCurrentPageChanged);
  }

  void Detach()
  {
    if(mPanel)
    {
      mPanel->CurrentPageChangedSignal().Disconnect(this, &PageBadge::OnCurrentPageChanged);
      mPanel = nullptr;
    }
  }

  uint32_t ConnectionCount() const
  {
    return GetConnectionCount();
  }

private:
  void OnCurrentPageChanged(int pageIndex)
  {
    mVisiblePage = pageIndex;
  }

private:
  DocumentPanel* mPanel{nullptr};
  int            mVisiblePage{0};
};
```

`Dali::Signal::Disconnect()` must receive the same callback shape that was connected. `Dali::ConnectionTracker::GetConnectionCount()` is useful for diagnostics in controller code, while `Dali::Signal::GetConnectionCount()` tells you how many callbacks are attached to a specific event.

## Emit Return-Value Signals

`Dali::Signal` also supports return values with signatures such as `Dali::Signal< Ret()>`, `Dali::Signal< Ret(Arg0)>`, `Dali::Signal< Ret(Arg0, Arg1)>`, and `Dali::Signal< Ret(Arg0, Arg1, Arg2)>`. When emitted, the public `Emit()` call returns the value from the signal implementation.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali/public-api/signals/dali-signal.h>

class ClosePolicy
{
public:
  using CanCloseSignalType = Dali::Signal<bool()>;

  CanCloseSignalType& CanCloseSignal()
  {
    return mCanCloseSignal;
  }

  bool CanClose()
  {
    return static_cast<Dali::Signal<bool()>&>(mCanCloseSignal).Emit();
  }

private:
  CanCloseSignalType mCanCloseSignal;
};

class CloseController : public Dali::ConnectionTracker
{
public:
  void Bind(ClosePolicy& policy)
  {
    policy.CanCloseSignal().Connect(this, &CloseController::OnCanClose);
  }

private:
  bool OnCanClose()
  {
    return mHasNoPendingChanges;
  }

private:
  bool mHasNoPendingChanges{true};
};
```

Use return-value signals for policy-style extension points where one answer is expected. Use `void` signals for notifications such as view readiness, page changes, or resource events.

## Use `BaseSignal` Only Behind Typed Signals

`Dali::BaseSignal` is the shared implementation used by typed `Dali::Signal` specializations. Application code should normally expose and consume typed `Dali::Signal` objects, because they preserve the callback argument and return types at the API boundary.

The typed signal methods forward to `Dali::BaseSignal` behavior such as `Emit`, `EmitReturn`, `EmitReturnOr`, `Empty`, and `GetConnectionCount`. `Dali::BaseSignal::EmitGuard` protects a signal while it is emitting so that nested emission on the same signal is detected internally.

```cpp
#include <dali/public-api/signals/dali-signal.h>

class LoadingState
{
public:
  using ProgressSignalType = Dali::Signal<void(float)>;

  ProgressSignalType& ProgressSignal()
  {
    return mProgressSignal;
  }

  void SetProgress(float progress)
  {
    mProgress = progress;
    static_cast<Dali::Signal<void(float)>&>(mProgressSignal).Emit(mProgress);
  }

  bool HasProgressListeners() const
  {
    return !static_cast<const Dali::Signal<void(float)>&>(mProgressSignal).Empty();
  }

private:
  float              mProgress{0.0f};
  ProgressSignalType mProgressSignal;
};
```

For dali-ui application code, prefer the typed `Dali::Signal` surface. Reach for lower-level symbols such as `Dali::BaseSignal`, `Dali::CallbackBase`, `Dali::SignalConnection`, `Dali::SignalConnectionPool`, `Dali::SlotConnection`, `Dali::SignalObserver`, and `Dali::SlotObserver` only when implementing or maintaining signal infrastructure rather than normal view-level app behavior.
