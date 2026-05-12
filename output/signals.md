---
title: Signals
sidebar_label: Signals
category: uncategorized
---

# Signals

Signals provide a type-safe publish-subscribe mechanism for event handling in dali-ui applications. They allow objects to emit events that multiple listeners can observe, with automatic connection management when slot owners are destroyed.

## Table of Contents

- [Connecting to Signals](#connecting-to-signals)
- [Emitting Signals](#emitting-signals)
- [Disconnecting from Signals](#disconnecting-from-signals)
- [Connection Lifetime Management](#connection-lifetime-management)
- [Signal Templates and Signatures](#signal-templates-and-signatures)

## Connecting to Signals

### Member Function Connection

The most common pattern is connecting a member function to a signal. The slot owner must inherit from `ConnectionTracker` for automatic disconnection when destroyed.

```cpp
class MyController : public ConnectionTracker
{
public:
  void OnInit(Application application)
  {
    Window window = application.GetWindow();

    // Connect to key event signal
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

### Connecting to View Signals

dali-ui Views expose signals for various events. The signal handler typically receives the source view as the first parameter.

```cpp
class WebViewExample : public ConnectionTracker
{
public:
  void Setup()
  {
    mWebView = WebView::New();

    // Connect to page load signals
    mWebView.PageLoadStartedSignal().Connect(this, &WebViewExample::OnPageLoadStarted);
    mWebView.PageLoadFinishedSignal().Connect(this, &WebViewExample::OnPageLoadFinished);
    mWebView.UrlChangedSignal().Connect(this, &WebViewExample::OnUrlChanged);
  }

  void OnPageLoadStarted(WebView view, const Dali::String& url)
  {
    // Handle page load start
  }

  void OnPageLoadFinished(WebView view, const Dali::String& url)
  {
    Dali::String title = view.GetTitle();
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

### Static Function Connection

Static functions can be connected without a `ConnectionTracker` instance.

```cpp
void OnApplicationTerminated()
{
  // Cleanup resources
}

// Connect static function
application.TerminatedSignal().Connect(&OnApplicationTerminated);
```

### SlotDelegate for Composition

When you prefer composition over inheritance, use `SlotDelegate` instead of inheriting from `ConnectionTracker`.

```cpp
class MyAnimationHandler
{
public:
  MyAnimationHandler()
  : mSlotDelegate(this)
  {
  }

  void PlayAnimation()
  {
    Animation animation = Animation::New(1.0f);
    animation.FinishedSignal().Connect(mSlotDelegate, &MyAnimationHandler::OnAnimationFinished);
    animation.Play();
  }

  void OnAnimationFinished(Animation& animation)
  {
    // Animation completed
  }

private:
  SlotDelegate<MyAnimationHandler> mSlotDelegate;
};
```

### Function Object Connection

Lambda expressions and function objects can be connected using a `ConnectionTrackerInterface`.

```cpp
class MyView : public ConnectionTracker
{
public:
  void Setup()
  {
    // Define a local signal for demonstration
    Signal<void(View, bool)> visibilitySignal;

    // Connect lambda with capture
    visibilitySignal.Connect(this, [this](View v, bool visible) {
      if(visible)
      {
        OnViewShown(v);
      }
    });
  }

  void OnViewShown(View view)
  {
    // Handle view visibility
  }
};
```

## Emitting Signals

### Basic Emission

Signals are emitted by calling `Emit()` with the appropriate arguments.

```cpp
// Define signal type using BaseSignal
typedef BaseSignal VoidSignalType;
typedef BaseSignal IntParamSignalType;
typedef BaseSignal BoolReturnSignalType;

// Emit signals
VoidSignalType voidSignal;
voidSignal.Emit();  // No arguments

IntParamSignalType intSignal;
intSignal.Emit(42);  // Pass argument

BoolReturnSignalType boolSignal;
boolSignal.Emit(1.5f, 10);  // Emit with arguments
```

### Return Value Handling

For signals with return values, `Emit()` returns the value from the last connected callback. If no callbacks are connected, a default-constructed value is returned.

```cpp
Signal<bool()> validationSignal;

// Connect multiple validators
validationSignal.Connect(this, &MyClass::ValidateInput);
validationSignal.Connect(this, &MyClass::ValidateConstraints);

// Emit returns result from last callback
bool isValid = validationSignal.Emit();
```

### EmitOr for Boolean Aggregation

For boolean return signals with multiple parameters, `EmitOr()` returns `true` if any callback returns `true`.

```cpp
Signal<bool(float, int)> touchSignal;

touchSignal.Connect(this, &MyClass::OnTouchArea1);
touchSignal.Connect(this, &MyClass::OnTouchArea2);

// Returns true if ANY handler returns true
bool consumed = touchSignal.EmitOr(x, y);
```

## Disconnecting from Signals

### Explicit Disconnection

Disconnect specific callbacks when they are no longer needed.

```cpp
class MyHandler : public ConnectionTracker
{
public:
  void Connect()
  {
    mSignal.Connect(this, &MyHandler::OnEvent);
  }

  void Disconnect()
  {
    mSignal.Disconnect(this, &MyHandler::OnEvent);
  }

  void OnEvent() {}

private:
  Signal<void()> mSignal;
};
```

### Disconnect All Connections

`ConnectionTracker` provides `DisconnectAll()` to remove all signal connections at once.

```cpp
class MyController : public ConnectionTracker
{
public:
  void Cleanup()
  {
    // Disconnect from all signals
    DisconnectAll();
  }
};
```

`SlotDelegate` also supports bulk disconnection:

```cpp
SlotDelegate<MyClass> mSlotDelegate;

// Disconnect all connections made through this delegate
mSlotDelegate.DisconnectAll();
```

### Querying Connection State

Check if a signal has connections or count active connections.

```cpp
Signal<void()> mySignal;

// Check if empty
if(mySignal.Empty())
{
  // No callbacks connected
}

// Get connection count
std::size_t count = mySignal.GetConnectionCount();
```

## Connection Lifetime Management

### Automatic Disconnection on Destruction

When a `ConnectionTracker`-derived object is destroyed, all its signal connections are automatically removed.

```cpp
{
  MyHandler handler;  // Inherits ConnectionTracker
  handler.Connect();

  // Signal has 1 connection
}  // handler destroyed - connection automatically removed

// Signal is now empty
```

### SlotDelegate Lifetime

`SlotDelegate` automatically disconnects when destroyed, making it safe for member objects.

```cpp
class MyComponent
{
  ~MyComponent()
  {
    // mSlotDelegate destructor disconnects all signals
  }

private:
  SlotDelegate<MyComponent> mSlotDelegate;
};
```

### Multiple Connections

Multiple callbacks can connect to the same signal. They are called in connection order during emission.

```cpp
Signal<void(int)> dataSignal;

dataSignal.Connect(this, &MyClass::OnDataFirst);
dataSignal.Connect(this, &MyClass::OnDataSecond);
dataSignal.Connect(this, &MyClass::OnDataThird);

dataSignal.Emit(42);  // Calls all three handlers in order
```

### Connection During Emission

Callbacks connected during signal emission are not called until the next emission cycle.

```cpp
Signal<void()> mySignal;

void Handler()
{
  // This connection won't fire during current emission
  mySignal.Connect(this, &MyClass::LateHandler);
}

mySignal.Connect(this, &MyClass::Handler);
mySignal.Emit();  // Only Handler() is called

mySignal.Emit();  // Now both Handler() and LateHandler() are called
```

## Signal Templates and Signatures

dali-ui supports signal templates with up to 3 parameters, with optional return values.

### Void Return Signals

```cpp
// No parameters
Signal<void()> simpleSignal;

// One parameter
Signal<void(int)> intParamSignal;

// Two parameters
Signal<void(float, float)> coordsSignal;

// Three parameters
Signal<void(int, int, int)> tripleSignal;
```

### Return Value Signals

```cpp
// No parameters, returns bool
Signal<bool()> validationSignal;

// One parameter, returns int
Signal<int(float)> computeSignal;

// Two parameters, returns bool
Signal<bool(float, float)> hitTestSignal;

// Three parameters, returns float
Signal<float(int, int, int)> weightedSignal;
```

### Common dali-ui Signal Patterns

View signals typically pass the source view as the first parameter:

```cpp
// View event signal pattern
typedef Signal<void(View)> ViewSignalType;
typedef Signal<void(View, const Property::Map&)> ViewDataSignalType;

// Touch/key event pattern
typedef Signal<bool(View, const TouchEvent&)> TouchSignalType;
typedef Signal<bool(View, const KeyEvent&)> KeySignalType;