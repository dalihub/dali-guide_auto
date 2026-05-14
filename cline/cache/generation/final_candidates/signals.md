---
title: Signals
sidebar_label: Signals
category: utilities
---

# Signals

Signals provide a type-safe publish-subscribe mechanism for responding to events in dali-ui applications. They enable loose coupling between event sources and handlers while ensuring automatic disconnection when slot owners are destroyed.

## Table of Contents

- [Connecting to Signals](#connecting-to-signals)
- [Emitting Signals](#emitting-signals)
- [Connection Lifecycle Management](#connection-lifecycle-management)
- [Signal Variants](#signal-variants)
- [Advanced Connection Patterns](#advanced-connection-patterns)

## Connecting to Signals

To receive signal emissions, connect a callback function or member function to the signal. The connection requires a `ConnectionTrackerInterface` implementation to manage the signal-slot connection lifetime.

### Member Function Connection

The most common pattern is inheriting from `ConnectionTracker` and connecting member functions:

```cpp
#include <dali/public-api/signals/connection-tracker.h>

class MyController : public ConnectionTracker
{
public:
  void OnInit(Application application)
  {
    Window window = application.GetWindow();
    
    // Connect to window key event signal
    window.KeyEventSignal().Connect(this, &MyController::OnKeyEvent);
  }

  void OnKeyEvent(Window window, KeyEvent event)
  {
    if(event.GetState() == KeyEvent::DOWN)
    {
      // Handle key press
    }
  }
};
```

The `ConnectionTracker` base class automatically disconnects all signals when the object is destroyed, preventing dangling callbacks.

### Static Function Connection

For simple callbacks without object context, connect static functions:

```cpp
void OnApplicationReady()
{
  // Handle application ready event
}

// Connect static function
someSignal.Connect(&OnApplicationReady);

// Later, disconnect when no longer needed
someSignal.Disconnect(&OnApplicationReady);
```

### View Signal Connection

dali-ui views expose signals for user interaction and state changes:

```cpp
class WebViewController : public ConnectionTracker
{
public:
  void SetupWebView()
  {
    mWebView = WebView::New();
    
    // Connect to page load signals
    mWebView.PageLoadStartedSignal().Connect(this, &WebViewController::OnPageLoadStarted);
    mWebView.PageLoadFinishedSignal().Connect(this, &WebViewController::OnPageLoadFinished);
    mWebView.UrlChangedSignal().Connect(this, &WebViewController::OnUrlChanged);
  }

  void OnPageLoadStarted(WebView view, const Dali::String& url)
  {
    // Handle page load start
  }

  void OnPageLoadFinished(WebView view, const Dali::String& url)
  {
    // Handle page load complete
  }

  void OnUrlChanged(WebView view, const Dali::String& url)
  {
    // Handle URL change
  }

private:
  WebView mWebView;
};
```

## Emitting Signals

Signals are emitted by calling the `Emit()` method with arguments matching the signal signature.

### Void Return Signals

For signals without return values:

```cpp
// Emit without arguments
Signal<void()> signal;
signal.Emit();

// Emit with arguments
Signal<void(int)> intSignal;
intSignal.Emit(42);
```

### Signals with Return Values

For signals that return a value, `Emit()` returns the last callback's return value:

```cpp
Signal<bool()> boolSignal;
bool result = boolSignal.Emit();  // Returns last callback result

Signal<int(float, int)> mixedSignal;
int value = mixedSignal.Emit(1.5f, 10);
```

### EmitOr for Boolean Aggregation

For boolean-returning signals with multiple callbacks, `EmitOr()` returns `true` if any callback returns `true`:

```cpp
Signal<bool(float, int)> signal;
signal.Connect(&Handler1, &Handler1::OnEvent);
signal.Connect(&Handler2, &Handler2::OnEvent);

// Returns true if any handler returns true
bool handled = signal.EmitOr(1.0f, 5);
```

Note: `EmitOr()` is only available for `Signal<Ret(Arg0, Arg1)>` — signals with exactly two parameters and a return value.

## Connection Lifecycle Management

### Automatic Disconnection

When a `ConnectionTracker`-derived object is destroyed, all its signal connections are automatically disconnected:

```cpp
class Controller : public ConnectionTracker
{
public:
  Controller()
  {
    // Connections tracked automatically
    someSignal.Connect(this, &Controller::OnEvent);
  }
  
  // Destructor automatically disconnects all connections
};
```

### Manual Disconnection

Disconnect individual callbacks explicitly:

```cpp
// Disconnect member function
signal.Disconnect(&handler, &HandlerClass::OnCallback);

// Disconnect static function
signal.Disconnect(&StaticCallback);
```

### Disconnect All Connections

Remove all connections from a `ConnectionTracker`:

```cpp
class MyController : public ConnectionTracker
{
public:
  void Reset()
  {
    // Disconnect all signals connected through this tracker
    DisconnectAll();
  }
};
```

### Querying Connection State

Check signal state before operations:

```cpp
Signal<void()> signal;

// Check if any connections exist
if(signal.Empty())
{
  // No callbacks connected
}

// Get connection count
uint32_t count = signal.GetConnectionCount();
```

## Signal Variants

Signals support up to three parameters with optional return values.

### Zero Parameters

```cpp
// void return
Signal<void()> voidSignal;
voidSignal.Connect(this, &MyClass::OnEvent);
voidSignal.Emit();

// with return value
Signal<int()> intSignal;
int result = intSignal.Emit();
```

### One Parameter

```cpp
// void return with one parameter
Signal<void(int)> voidIntSignal;
voidIntSignal.Connect(this, &MyClass::OnValue);
voidIntSignal.Emit(42);

// with return value
Signal<bool(const std::string&)> boolStringSignal;
bool result = boolStringSignal.Emit("test");
```

### Two Parameters

```cpp
// void return with two parameters
Signal<void(float, float)> positionSignal;
positionSignal.Connect(this, &MyClass::OnPosition);
positionSignal.Emit(100.0f, 200.0f);

// with return value
Signal<int(float, int)> mixedSignal;
int value = mixedSignal.Emit(1.5f, 10);
```

### Three Parameters

```cpp
// void return with three parameters
Signal<void(float, float, float)> colorSignal;
colorSignal.Connect(this, &MyClass::OnColor);
colorSignal.Emit(1.0f, 0.5f, 0.0f);

// with return value
Signal<int(float, float, float)> threeParamReturnSignal;
int result = threeParamReturnSignal.Emit(1.0f, 2.0f, 3.0f);
```

## Advanced Connection Patterns

### SlotDelegate for Composition

When inheritance from `ConnectionTracker` is not desired, use `SlotDelegate`:

```cpp
#include <dali/public-api/signals/slot-delegate.h>

class MyComponent
{
public:
  void ConnectSignals(Signal<void()>& signal)
  {
    signal.Connect(mSlotDelegate, &MyComponent::OnEvent);
  }

  void DisconnectAll()
  {
    mSlotDelegate.DisconnectAll();
  }

  uint32_t GetConnectionCount() const
  {
    return mSlotDelegate.GetConnectionCount();
  }

private:
  void OnEvent()
  {
    // Handle event
  }

  SlotDelegate<MyComponent> mSlotDelegate;
};
```

### Functor Objects

Connect lambda expressions or functor objects:

```cpp
class MyController : public ConnectionTracker
{
public:
  void SetupHandler()
  {
    Signal<void(int)> signal;
    
    // Connect lambda
    signal.Connect(this, [this](int value) {
      OnValueReceived(value);
    });
  }

  void OnValueReceived(int value)
  {
    // Handle value
  }
};
```

### Multiple Connections

A signal can have multiple callbacks connected. They are called in connection order:

```cpp
Signal<void()> signal;

signal.Connect(&Handler1, &Handler1::OnEvent);
signal.Connect(&Handler2, &Handler2::OnEvent);
signal.Connect(&Handler3, &Handler3::OnEvent);

// All three handlers called in order
signal.Emit();
```

### Connection During Emission

Callbacks connected during signal emission are not called until the next emission:

```cpp
Signal<void()> signal;
signal.Connect(this, &MyClass::FirstHandler);

void FirstHandler()
{
  // This connection will not fire during this emission cycle
  signal.Connect(this, &MyClass::SecondHandler);
}

signal.Emit();  // Only FirstHandler called
signal.Emit();  // Both FirstHandler and SecondHandler called
```

### Disconnection During Emission

Callbacks can safely disconnect themselves during emission:

```cpp
void OnEvent()
{
  // Safe to disconnect during callback
  signal.Disconnect(this, &MyClass::OnEvent);
}
