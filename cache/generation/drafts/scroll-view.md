--- title: Scroll View
sidebar_label: Scroll View
category: views-components
---

# Scroll View

`ScrollView` is a scrollable container that displays content larger than its viewport, with pan gesture support and configurable fling behavior.

## Table of Contents

- [Creating a Scroll View](#creating-a-scroll-view)
- [Setting Content](#setting-content)
- [Scroll Direction](#scroll-direction)
- [Programmatic Scrolling](#programmatic-scrolling)
- [Fling Behavior](#fling-behavior)
- [Scroll Bar Visibility](#scroll-bar-visibility)
- [Over-Scroll Mode](#over-scroll-mode)
- [Scroll Signals](#scroll-signals)

## Creating a Scroll View

Create a `ScrollView` using the static `ScrollView::New()` method. The returned handle can be configured using method chaining.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void CreateScrollView(Application application)
{
  Window window = application.GetWindow();

  ScrollView scrollView = ScrollView::New()
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetRequestedWidth(480.0f)
    .SetRequestedHeight(800.0f);

  window.Add(scrollView);
}
```

## Setting Content

The content view contains the scrollable elements. Set it using `SetContent()` and retrieve it with `GetContent()`. The content size should exceed the `ScrollView` viewport to enable scrolling.

```cpp
void SetupContent(ScrollView& scrollView)
{
  // Create a layout as content
  StackLayout content = StackLayout::New(StackOrientation::VERTICAL);
  content.SetRequestedWidth(480.0f);
  content.SetRequestedHeight(1600.0f);  // Larger than viewport
  content.SetSpacing(10.0f);
  content.SetPadding(Extents(20, 20, 20, 20));

  // Add child views to content
  for (int i = 0; i < 10; ++i)
  {
    View item = View::New();
    item.SetRequestedWidth(440.0f);
    item.SetRequestedHeight(120.0f);
    item.SetBackgroundColor(Color::BLUE);
    content.Add(item);
  }

  scrollView.SetContent(content);
}
```

To retrieve the current content:

```cpp
View content = scrollView.GetContent();
```

## Scroll Direction

Control the scrolling axis using `SetScrollDirection()` with one of the `ScrollDirection` enum values:

- `ScrollDirection::Vertical` — Vertical scrolling only
- `ScrollDirection::Horizontal` — Horizontal scrolling only
- `ScrollDirection::Both` — Both horizontal and vertical scrolling

```cpp
ScrollView scrollView = ScrollView::New()
  .SetScrollDirection(ScrollDirection::Vertical);

// Query current direction
ScrollDirection direction = scrollView.GetScrollDirection();
```

## Programmatic Scrolling

Use `ScrollTo()`, `ScrollToX()`, or `ScrollToY()` to scroll programmatically. Each method accepts an optional `animation` parameter (default `true`).

```cpp
void ScrollToPosition(ScrollView& scrollView)
{
  // Scroll to a specific position with animation
  scrollView.ScrollTo(Vector2(0.0f, 500.0f));

  // Scroll without animation
  scrollView.ScrollTo(Vector2(0.0f, 500.0f), false);

  // Scroll horizontally only
  scrollView.ScrollToX(200.0f);

  // Scroll vertically only
  scrollView.ScrollToY(300.0f, true);
}
```

To scroll to a specific child view, use the `ScrollTo()` overload with a child view and scroll position:

```cpp
void ScrollToChild(ScrollView& scrollView, View child)
{
  // Scroll to make the child visible
  scrollView.ScrollTo(child);

  // Scroll to center the child
  scrollView.ScrollTo(child, true, ScrollToPosition::Center);
}
```

The `ScrollToPosition` enum controls where the child appears:
- `ScrollToPosition::Start` — Scroll to start of view
- `ScrollToPosition::Center` — Scroll to center of view
- `ScrollToPosition::End` — Scroll to end of view
- `ScrollToPosition::MakeVisible` — Scroll to make the view visible (default)

Get the current scroll position using `GetScrollPosition()`:

```cpp
Vector2 position = scrollView.GetScrollPosition();
float currentY = position.y;
```

Set the scroll position directly without animation using `SetScrollPosition()`:

```cpp
scrollView.SetScrollPosition(Vector2(0.0f, 100.0f));
```

Check if scrolling is in progress:

```cpp
if (scrollView.IsScrolling())
{
  // Scrolling animation is active
}
```

## Fling Behavior

Configure inertia scrolling behavior after a pan gesture ends.

### Fling Distance

Control the maximum distance traveled during a fling:

```cpp
scrollView.SetMaxFlingDistance(6000.0f);
float maxDistance = scrollView.GetMaxFlingDistance();
```

### Fling Duration

Set the minimum and maximum duration of fling animations:

```cpp
scrollView.SetMinimumFlingDuration(1000);  // 1 second minimum
scrollView.SetMaximumFlingDuration(2000);  // 2 seconds maximum

int minDuration = scrollView.GetMinimumFlingDuration();
int maxDuration = scrollView.GetMaximumFlingDuration();
```

### Fling Sensitivity

Adjust how sensitive the scroll view is to fling gestures:

```cpp
scrollView.SetFlingSensitivity(1.0f);  // Default sensitivity
float sensitivity = scrollView.GetFlingSensitivity();
```

### Deceleration Rate

Control how quickly the scroll decelerates after a fling:

```cpp
scrollView.SetDecelerationRate(0.998f);  // Higher = slower deceleration
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

## Scroll Bar Visibility

Control scroll bar visibility using `ScrollBarVisibility` enum values:

- `ScrollBarVisibility::Auto` — Show only during scrolling
- `ScrollBarVisibility::Always` — Always visible
- `ScrollBarVisibility::Never` — Never visible

```cpp
ScrollView scrollView = ScrollView::New()
  .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
  .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);

// Query current visibility
ScrollBarVisibility vVis = scrollView.GetVerticalScrollBarVisibility();
ScrollBarVisibility hVis = scrollView.GetHorizontalScrollBarVisibility();
```

## Over-Scroll Mode

Configure behavior when scrolling past content boundaries using `OverScrollMode`:

- `OverScrollMode::Never` — Never allow over-scrolling
- `OverScrollMode::Always` — Always allow over-scrolling
- `OverScrollMode::ContentScrolls` — Allow only when content is scrollable

```cpp
scrollView.SetOverScrollMode(OverScrollMode::ContentScrolls);
OverScrollMode mode = scrollView.GetOverScrollMode();
```

## Scroll Signals

Connect to scroll events to respond to user interaction.

### Scroll Lifecycle Signals

- `ScrollStartedSignal()` — Emitted when scrolling begins
- `ScrollingSignal()` — Emitted continuously during scrolling
- `ScrollFinishedSignal()` — Emitted when scrolling ends

```cpp
class MyController : public ConnectionTracker
{
public:
  void SetupScrollView(ScrollView& scrollView)
  {
    scrollView.ScrollStartedSignal().Connect(this, &MyController::OnScrollStarted);
    scrollView.ScrollingSignal().Connect(this, &MyController::OnScrolling);
    scrollView.ScrollFinishedSignal().Connect(this, &MyController::OnScrollFinished);
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
    // Handle scroll end
  }
};
```

### Drag Signals

- `DragStartedSignal()` — Emitted when a drag gesture begins
- `DraggingSignal()` — Emitted during drag with delta values
- `DragFinishedSignal()` — Emitted when the drag gesture ends

```cpp
class MyDragHandler : public ConnectionTracker
{
public:
  void ConnectDragSignals(ScrollView& scrollView)
  {
    scrollView.DragStartedSignal().Connect(this, &MyDragHandler::OnDragStarted);
    scrollView.DraggingSignal().Connect(this, &MyDragHandler::OnDragging);
    scrollView.DragFinishedSignal().Connect(this, &MyDragHandler::OnDragFinished);
  }

  void OnDragStarted(ScrollView scrollView)
  {
    // Drag gesture started
  }

  void OnDragging(ScrollView scrollView, float deltaX, float deltaY)
  {
    // Dragging with delta movement
  }

  void OnDragFinished(ScrollView scrollView)
  {
    // Drag gesture ended
  }
};
```

## Complete Example

The following example demonstrates a vertically scrolling list with scroll bars and signal handling:

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

    // Create scrollable content
    StackLayout content = StackLayout::New(StackOrientation::VERTICAL);
    content.SetRequestedWidth(480.0f);
    content.SetRequestedHeight(2000.0f);
    content.SetSpacing(8.0f);

    for (int i = 0; i < 12; ++i)
    {
      View item = View::New();
      item.SetBackgroundColor(Color::BLUE);
      item.SetRequestedWidth(480.0f);
      item.SetRequestedHeight(150.0f);
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
      .SetRequestedWidth(480.0f)
      .SetRequestedHeight(800.0f)
      .SetContent(content);

    // Connect signals
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
```

## Downcasting

Use `ScrollView::DownCast()` to safely convert a `BaseHandle` to a `ScrollView`:

```cpp
ScrollView scrollView = ScrollView::New();
BaseHandle handle = scrollView;

ScrollView casted = ScrollView::DownCast(handle);
if (casted)
{
  // Downcast successful
}