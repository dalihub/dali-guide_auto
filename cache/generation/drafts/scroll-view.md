---
title: Scroll View
sidebar_label: Scroll View
category: uncategorized
---

# Scroll View

`ScrollView` is a scrollable container that displays content larger than its viewport, providing pan gesture support for navigating content that exceeds the view's visible area.

## Table of Contents

- [Creating a Scroll View](#creating-a-scroll-view)
- [Setting Content](#setting-content)
- [Configuring Scroll Direction](#configuring-scroll-direction)
- [Programmatic Scrolling](#programmatic-scrolling)
- [Fling Behavior](#fling-behavior)
- [Scroll Bar Visibility](#scroll-bar-visibility)
- [Over-Scroll Mode](#over-scroll-mode)
- [Scroll Events](#scroll-events)
- [Drag Events](#drag-events)

---

## Creating a Scroll View

Create a `ScrollView` using the static `New()` method. The returned handle can be configured using method chaining.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

// Create a ScrollView with basic configuration
ScrollView scrollView = ScrollView::New()
  .SetScrollDirection(ScrollDirection::Vertical)
  .SetRequestedWidth(480.0f)
  .SetRequestedHeight(800.0f);

// Add to window
window.Add(scrollView);
```

Use `ScrollView::DownCast()` to safely downcast a `BaseHandle` to a `ScrollView`:

```cpp
BaseHandle handle = scrollView; // ScrollView inherits from View
ScrollView casted = ScrollView::DownCast(handle);
if (casted)
{
  // Successfully downcast
}
```

---

## Setting Content

The content view is the scrollable area that can be larger than the `ScrollView` viewport. Use `SetContent()` to assign a content view and `GetContent()` to retrieve it.

```cpp
// Create content larger than the scroll view
StackLayout content = StackLayout::New(StackOrientation::VERTICAL);
content.SetRequestedWidth(MATCH_PARENT);
content.SetRequestedHeight(2400.0f); // Must be explicit; ScrollView does not constrain content height
content.SetSpacing(10.0f);
content.SetPadding(Extents(20, 20, 20, 20));

// Add child views to content
for (int i = 0; i < 12; ++i)
{
  View item = View::New()
    .SetBackgroundColor(Color::BLUE)
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(180.0f);
  content.Add(item);
}

// Set content to scroll view
scrollView.SetContent(content);

// Retrieve content later
View retrievedContent = scrollView.GetContent();
```

`SetContent()` returns a reference to the `ScrollView`, enabling method chaining:

```cpp
ScrollView scrollView = ScrollView::New()
  .SetScrollDirection(ScrollDirection::Vertical)
  .SetContent(content);
```

---

## Configuring Scroll Direction

`ScrollView` supports three scroll directions via `SetScrollDirection()`:

- `ScrollDirection::Vertical` — Vertical scrolling only
- `ScrollDirection::Horizontal` — Horizontal scrolling only
- `ScrollDirection::Both` — Both horizontal and vertical scrolling

```cpp
// Vertical scrolling only
scrollView.SetScrollDirection(ScrollDirection::Vertical);

// Horizontal scrolling only
scrollView.SetScrollDirection(ScrollDirection::Horizontal);

// Bidirectional scrolling
scrollView.SetScrollDirection(ScrollDirection::Both);

// Query current direction
ScrollDirection direction = scrollView.GetScrollDirection();
```

---

## Programmatic Scrolling

Use `ScrollTo()`, `ScrollToX()`, and `ScrollToY()` to scroll programmatically. All methods accept an optional `animation` parameter (default is `true`).

### Scroll to Position

```cpp
// Scroll to a specific position with animation
scrollView.ScrollTo(Vector2(100.0f, 200.0f));

// Scroll without animation
scrollView.ScrollTo(Vector2(100.0f, 200.0f), false);
```

### Scroll to X or Y Coordinate

```cpp
// Horizontal scroll
scrollView.ScrollToX(150.0f);        // With animation
scrollView.ScrollToX(150.0f, false); // Without animation

// Vertical scroll
scrollView.ScrollToY(300.0f);        // With animation
scrollView.ScrollToY(300.0f, false);  // Without animation
```

### Get Current Scroll Position

```cpp
Vector2 position = scrollView.GetScrollPosition();
float x = position.x;
float y = position.y;
```

### Set Scroll Position Directly

Use `SetScrollPosition()` to set the position without animation:

```cpp
scrollView.SetScrollPosition(Vector2(50.0f, 100.0f));
```

### Check Scrolling State

```cpp
if (scrollView.IsScrolling())
{
  // Scroll animation is in progress
}
```

---

## Fling Behavior

Configure inertia scrolling behavior after the user releases a pan gesture.

### Fling Sensitivity

Controls how far a fling travels relative to gesture velocity. Default is `1.0f`.

```cpp
scrollView.SetFlingSensitivity(1.5f); // Increase fling distance
float sensitivity = scrollView.GetFlingSensitivity();
```

### Maximum Fling Distance

Caps the maximum distance a fling can travel.

```cpp
scrollView.SetMaxFlingDistance(6000.0f);
float maxDistance = scrollView.GetMaxFlingDistance();
```

### Fling Duration

Set the minimum and maximum duration of fling animations (in milliseconds).

```cpp
scrollView.SetMinimumFlingDuration(1000); // 1 second minimum
scrollView.SetMaximumFlingDuration(2000); // 2 seconds maximum

int minDuration = scrollView.GetMinimumFlingDuration();
int maxDuration = scrollView.GetMaximumFlingDuration();
```

### Deceleration Rate

Controls how quickly fling scrolling decelerates. Values closer to `1.0f` result in longer deceleration. Default is typically around `0.998f`.

```cpp
scrollView.SetDecelerationRate(0.998f);
float rate = scrollView.GetDecelerationRate();
```

### Complete Fling Configuration Example

```cpp
ScrollView scrollView = ScrollView::New()
  .SetScrollDirection(ScrollDirection::Vertical)
  .SetMaxFlingDistance(6000.0f)
  .SetMinimumFlingDuration(1000)
  .SetMaximumFlingDuration(2000)
  .SetFlingSensitivity(1.0f)
  .SetDecelerationRate(0.998f);
```

---

## Scroll Bar Visibility

Control scroll bar visibility independently for vertical and horizontal directions using `ScrollBarVisibility`:

- `ScrollBarVisibility::Auto` — Show scroll bar only when scrolling
- `ScrollBarVisibility::Always` — Always show scroll bar
- `ScrollBarVisibility::Never` — Never show scroll bar

```cpp
ScrollView scrollView = ScrollView::New()
  .SetScrollDirection(ScrollDirection::Vertical)
  .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
  .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);

// Query visibility settings
ScrollBarVisibility vVis = scrollView.GetVerticalScrollBarVisibility();
ScrollBarVisibility hVis = scrollView.GetHorizontalScrollBarVisibility();
```

---

## Over-Scroll Mode

Configure the over-scroll (bounce) behavior using `OverScrollMode`:

- `OverScrollMode::Never` — Never allow over-scrolling
- `OverScrollMode::Always` — Always allow over-scrolling
- `OverScrollMode::ContentScrolls` — Allow over-scrolling only when content is scrollable

```cpp
scrollView.SetOverScrollMode(OverScrollMode::ContentScrolls);
OverScrollMode mode = scrollView.GetOverScrollMode();
```

---

## Scroll Events

Connect to scroll signals to respond to scroll state changes. All scroll signals pass the `ScrollView` instance to the callback.

### Signal Types

| Signal | Description |
|--------|-------------|
| `ScrollStartedSignal()` | Emitted when scroll begins |
| `ScrollingSignal()` | Emitted continuously during scrolling |
| `ScrollFinishedSignal()` | Emitted when scroll completes |

### Connecting to Scroll Signals

```cpp
class MyController : public ConnectionTracker
{
public:
  void SetupScrollView()
  {
    mScrollView = ScrollView::New();
    mScrollView.SetScrollDirection(ScrollDirection::Vertical);

    // Connect scroll signals
    mScrollView.ScrollStartedSignal().Connect(this, &MyController::OnScrollStarted);
    mScrollView.ScrollingSignal().Connect(this, &MyController::OnScrolling);
    mScrollView.ScrollFinishedSignal().Connect(this, &MyController::OnScrollFinished);
  }

  void OnScrollStarted(ScrollView scrollView)
  {
    Vector2 pos = scrollView.GetScrollPosition();
    // Handle scroll start
  }

  void OnScrolling(ScrollView scrollView)
  {
    Vector2 pos = scrollView.GetScrollPosition();
    // Handle ongoing scroll
  }

  void OnScrollFinished(ScrollView scrollView)
  {
    Vector2 pos = scrollView.GetScrollPosition();
    // Handle scroll completion
  }

private:
  ScrollView mScrollView;
};
```

---

## Drag Events

Drag signals provide lower-level feedback about user pan gestures.

### Signal Types

| Signal | Description | Callback Signature |
|--------|-------------|-------------------|
| `DragStartedSignal()` | Emitted when drag gesture starts | `void(ScrollView)` |
| `DraggingSignal()` | Emitted during drag | `void(ScrollView, float deltaX, float deltaY)` |
| `DragFinishedSignal()` | Emitted when drag gesture ends | `void(ScrollView)` |

### Connecting to Drag Signals

```cpp
class MyController : public ConnectionTracker
{
public:
  void SetupScrollView()
  {
    mScrollView = ScrollView::New();

    mScrollView.DragStartedSignal().Connect(this, &MyController::OnDragStarted);
    mScrollView.DraggingSignal().Connect(this, &MyController::OnDragging);
    mScrollView.DragFinishedSignal().Connect(this, &MyController::OnDragFinished);
  }

  void OnDragStarted(ScrollView scrollView)
  {
    // User started dragging
  }

  void OnDragging(ScrollView scrollView, float deltaX, float deltaY)
  {
    // User is dragging - deltaX and deltaY indicate drag delta
  }

  void OnDragFinished(ScrollView scrollView)
  {
    // User released drag
  }

private:
  ScrollView mScrollView;
};
```

---

## Complete Example

The following example demonstrates a complete `ScrollView` setup with content, configuration, and signal handling:

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

class ScrollViewController : public ConnectionTracker
{
public:
  ScrollViewController(Application& application)
    : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &ScrollViewController::Create);
  }

  void Create(Application application)
  {
    Window window = application.GetWindow();
    window.SetBackgroundColor(Color::WHITE);

    // Create content with multiple items
    StackLayout content = StackLayout::New(StackOrientation::VERTICAL);
    content.SetRequestedWidth(MATCH_PARENT);
    content.SetRequestedHeight(2400.0f);
    content.SetSpacing(10.0f);
    content.SetPadding(Extents(20, 20, 20, 20));

    for (int i = 0; i < 12; ++i)
    {
      View item = View::New()
        .SetBackgroundColor(Color::BLUE)
        .SetRequestedWidth(MATCH_PARENT)
        .SetRequestedHeight(180.0f);
      content.Add(item);
    }

    // Create and configure ScrollView
    mScrollView = ScrollView::New()
      .SetScrollDirection(ScrollDirection::Vertical)
      .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
      .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never)
      .SetMaxFlingDistance(6000.0f)
      .SetMinimumFlingDuration(1000)
      .SetMaximumFlingDuration(2000)
      .SetFlingSensitivity(1.0f)
      .SetDecelerationRate(0.998f)
      .SetOverScrollMode(OverScrollMode::ContentScrolls)
      .SetRequestedWidth(600.0f)
      .SetRequestedHeight(1200.0f)
      .SetContent(content);

    // Connect scroll signals
    mScrollView.ScrollStartedSignal().Connect(this, &ScrollViewController::OnScrollStarted);
    mScrollView.ScrollFinishedSignal().Connect(this, &ScrollViewController::OnScrollFinished);

    window.Add(mScrollView);
  }

  void OnScrollStarted(ScrollView scrollView)
  {
    Vector2 pos = scrollView.GetScrollPosition();
    // Handle scroll start
  }

  void OnScrollFinished(ScrollView scrollView)
  {
    Vector2 pos = scrollView.GetScrollPosition();
    // Handle scroll finish
  }

private:
  Application& mApplication;
  ScrollView mScrollView;
};

int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);
  ScrollViewController controller(application);
  application.MainLoop();
  return 0;
}
