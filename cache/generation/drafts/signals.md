---
title: Signals
sidebar_label: Signals
category: utilities
---

# Signals

Signals provide a type-safe publish-subscribe mechanism that allows objects to emit events that other objects can subscribe to. The signal-slot pattern enables loose coupling between event sources and handlers.

## Table of Contents

- [Connecting to Signals](#connecting-to-signals)
- [Emitting Signals](#emitting-signals)
- [Connection Management](#connection-management)
- [Signal Variants](#signal-variants)

## Connecting to Signals

To receive signal notifications, connect a callback function or member function to the signal. The connection requires a `ConnectionTrackerInterface` implementation to manage the connection lifecycle.

### Member Function Connection

The most common pattern is to inherit from `ConnectionTracker` and connect member functions:

```cpp
class MyController : public ConnectionTracker
{
public:
  void OnInit(Application application)
  {
    Window window = application.GetWindow();
    window.KeyEventSignal().Connect(this, &MyController::OnKeyEvent);
  }

  void OnKeyEvent(Window window, KeyEvent event)
  {
    // Handle key event
  }
};
```

### Static Function Connection

Connect free or static functions directly without a connection tracker:

```cpp
void OnButtonPressed()
{
  // Handle button press
}

// Connect static function
button.PressedSignal().Connect(&OnButtonPressed);

// Disconnect when done
button.PressedSignal().Disconnect(&OnButtonPressed);
```

### SlotDelegate Connection

Use `SlotDelegate` when you cannot inherit from `ConnectionTracker`:

```cpp
class MyHandler
{
public:
  SlotDelegate<MyHandler> mSlotDelegate;

  void ConnectToSignal(Signal<void()>& signal)
  {
    signal.Connect(mSlotDelegate, &MyHandler::OnSignal);
  }

  void OnSignal()
  {
    // Handle signal
  }
};
```

### Functor Connection

Connect lambda expressions or function objects:

```cpp
class MyController : public ConnectionTracker
{
public:
  void SetupHandler(Button& button)
  {
    button.PressedSignal().Connect(this, [](Button b) {
      // Handle button press
    });
  }
};
```

## Emitting Signals

Signals are emitted by calling the `Emit()` method with arguments matching the signal signature.

### Void Return Signals

For signals without return values, use `Emit()`:

```cpp
// Signal with no parameters
typedef Signal<void()> VoidSignalType;
VoidSignalType signal;
signal.Emit();

// Signal with one parameter
typedef Signal<void(int)> IntSignalType;
IntSignalType intSignal;
intSignal.Emit(42);

// Signal with two parameters
typedef Signal<void(float, float)> FloatPairSignalType;
FloatPairSignalType floatSignal;
floatSignal.Emit(1.0f, 2.0f);
```

### Signals with Return Values

For signals that return values, `Emit()` returns the last callback's result:

```cpp
typedef Signal<bool()> BoolSignalType;
BoolSignalType boolSignal;

bool result = boolSignal.Emit();
// Returns true if any callback returns true, false otherwise
```

For boolean signals with multiple callbacks, use `EmitOr()` to return `true` if any callback returns `true`:

```cpp
typedef Signal<bool(float, int)> BoolResultSignalType;
BoolResultSignalType signal;

// Returns true if any connected callback returns true
bool consumed = signal.EmitOr(1.0f, 10);
```

## Connection Management

### Automatic Disconnection

When a `ConnectionTracker` is destroyed, all its connections are automatically disconnected:

```cpp
class MyController : public ConnectionTracker
{
public:
  void Connect(Button& button)
  {
    button.PressedSignal().Connect(this, &MyController::OnPressed);
  }

  ~MyController()
  {
    // All connections automatically disconnected
  }
};
```

### Manual Disconnection

Explicitly disconnect individual callbacks:

```cpp
// Disconnect member function
signal.Disconnect(this, &MyClass::OnCallback);

// Disconnect static function
signal.Disconnect(&StaticCallback);
```

### Querying Connection State

Check if a signal has connected slots:

```cpp
if (signal.Empty())
{
  // No callbacks connected
}

uint32_t count = signal.GetConnectionCount();
```

### Disconnecting All Connections

Use `SlotDelegate::DisconnectAll()` to disconnect all connections from a delegate:

```cpp
SlotDelegate<MyHandler> mSlotDelegate;
mSlotDelegate.DisconnectAll();
```

## Signal Variants

DALi provides signal templates for different parameter counts and return types.

### No Parameters

```cpp
// void return, no parameters
Signal<void()> voidSignal;

// with return value
Signal<bool()> boolSignal;
Signal<int()> intSignal;
```

### One Parameter

```cpp
// void return, one parameter
Signal<void(int)> intParamSignal;

// with return value
Signal<bool(float)> floatResultSignal;
```

### Two Parameters

```cpp
// void return, two parameters
Signal<void(float, float)> twoFloatSignal;

// with return value
Signal<bool(int, int)> intPairResultSignal;
```

### Three Parameters

```cpp
// void return, three parameters
Signal<void(float, float, float)> threeFloatSignal;

// with return value
Signal<int(float, float, float)> threeFloatResultSignal;
```

### Defining Custom Signals

To add signals to your own classes, define signal typedefs and provide accessor methods:

```cpp
class MyButton : public View
{
public:
  // Signal typedefs
  typedef Signal<void()> PressedSignalType;
  typedef Signal<void(int)> ValueChangedSignalType;

  // Signal accessors
  PressedSignalType& PressedSignal();
  ValueChangedSignalType& ValueChangedSignal();

private:
  PressedSignalType mPressedSignal;
  ValueChangedSignalType mValueChangedSignal;
};
```

In the implementation:

```cpp
MyButton::PressedSignalType& MyButton::PressedSignal()
{
  return mPressedSignal;
}

void MyButton::EmitPressed()
{
  mPressedSignal.Emit();
}