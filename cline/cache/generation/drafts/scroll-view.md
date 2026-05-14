---
title: Scroll View
sidebar_label: Scroll View
category: views-components
---

# Scroll View

ScrollView is a scrollable container that displays content larger than its viewport, providing pan gesture support for navigating content that exceeds the view's visible area.

## Table of Contents

- [Creating a ScrollView](#creating-a-scrollview)
- [Setting Content](#setting-content)
- [Configuring Scroll Direction](#configuring-scroll-direction)
- [Programmatic Scrolling](#programmatic-scrolling)
- [Fling Behavior](#fling-behavior)
- [Scroll Bars](#scroll-bars)
- [Over-Scroll Mode](#over-scroll-mode)
- [Responding to Scroll Events](#responding-to-scroll-events)

## Creating a ScrollView

Create a ScrollView using the `ScrollView::New()` static method. The returned handle supports fluent chaining for configuration.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

// Create and configure a ScrollView
ScrollView scrollView = ScrollView::New()
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetRequestedWidth(600.0f)
    .SetRequestedHeight(800.0f);
```

ScrollView inherits from `View`, so you can add it to any parent view or window using `Add()`.

## Setting Content

The content view is the scrollable area that can be larger than the ScrollView's viewport. Use `SetContent()` to assign a content view.

```cpp
// Create a content layout larger than the scroll view
StackLayout content = StackLayout::New(StackOrientation::VERTICAL);
content.SetRequestedWidth(MATCH_PARENT);
content.SetRequestedHeight(WRAP_CONTENT);
content.SetSpacing(10.0f);
content.SetPadding(Extents(20, 20, 20, 20));

// Add child views to the content
for (int i = 0; i < 12; ++i)
{
    View item = View::New()
        .SetBackgroundColor(Color::BLUE)
        .SetRequestedWidth(MATCH_PARENT)
        .SetRequestedHeight(180.0f);
    content.Add(item);
}

// Set the content on the ScrollView
scrollView.SetContent(content);
```

Retrieve the current content using `GetContent()`:

```cpp
View currentContent = scrollView.GetContent();
```

## Configuring Scroll Direction

ScrollView supports three scroll directions via the `ScrollDirection` enum:

- `ScrollDirection::Vertical` — Vertical scrolling only
- `ScrollDirection::Horizontal` — Horizontal scrolling only
- `ScrollDirection::Both` — Both horizontal and vertical scrolling

```cpp
scrollView.SetScrollDirection(ScrollDirection::Vertical);
```

Query the current direction with `GetScrollDirection()`:

```cpp
ScrollDirection direction = scrollView.GetScrollDirection();
```

## Programmatic Scrolling

Control scroll position programmatically using `ScrollTo()`, `ScrollToX()`, and `ScrollToY()` methods.

### Scrolling to a Position

Use `ScrollTo()` to scroll to a specific `Vector2` position. The optional second parameter controls animation.

```cpp
// Scroll to position with animation
scrollView.ScrollTo(Vector2(0.0f, 500.0f));

// Scroll to position without animation
scrollView.ScrollTo(Vector2(0.0f, 500.0f), false);
```

For single-axis scrolling, use `ScrollToX()` or `ScrollToY()`:

```cpp
scrollView.ScrollToY(300.0f);        // With animation
scrollView.ScrollToY(300.0f, false); // Without animation
```

### Getting the Current Position

Retrieve the current scroll position using `GetScrollPosition()`:

```cpp
Vector2 position = scrollView.GetScrollPosition();
float currentY = position.y;
```

### Setting Position Directly

Use `SetScrollPosition()` to set the position without triggering a scroll animation:

```cpp
scrollView.SetScrollPosition(Vector2(0.0f, 100.0f));
```

### Checking Scroll State

Determine if scrolling is currently in progress with `IsScrolling()`:

```cpp
if (scrollView.IsScrolling())
{
    // Scroll animation is running
}
```

## Fling Behavior

ScrollView supports fling (inertia) scrolling with configurable physics parameters.

### Maximum Fling Distance

Control the maximum distance a fling can travel using `SetMaxFlingDistance()`:

```cpp
scrollView.SetMaxFlingDistance(6000.0f);
```

### Fling Duration

Set the minimum and maximum duration for fling animations:

```cpp
scrollView.SetMinimumFlingDuration(1000); // 1 second minimum
scrollView.SetMaximumFlingDuration(2000); // 2 seconds maximum
```

### Fling Sensitivity

Adjust how sensitive the fling response is to gesture velocity:

```cpp
scrollView.SetFlingSensitivity(1.0f); // Default sensitivity
scrollView.SetFlingSensitivity(1.5f); // More sensitive
scrollView.SetFlingSensitivity(0.5f); // Less sensitive
```

### Deceleration Rate

Control how quickly fling scrolling decelerates. Values closer to 1.0 result in longer fling distances.

```cpp
scrollView.SetDecelerationRate(0.998f); // Slower deceleration, longer fling
scrollView.SetDecelerationRate(0.95f);  // Faster deceleration, shorter fling
```

Retrieve the current deceleration rate:

```cpp
float rate = scrollView.GetDecelerationRate();
```

## Scroll Bars

Configure scroll bar visibility independently for vertical and horizontal axes using the `ScrollBarVisibility` enum:

- `ScrollBarVisibility::Auto` — Show scroll bar only when scrolling
- `ScrollBarVisibility::Always` — Always show scroll bar
- `ScrollBarVisibility::Never` — Never show scroll bar

```cpp
ScrollView scrollView = ScrollView::New()
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
    .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);
```

Query current visibility settings:

```cpp
ScrollBarVisibility vVisibility = scrollView.GetVerticalScrollBarVisibility();
ScrollBarVisibility hVisibility = scrollView.GetHorizontalScrollBarVisibility();
```

## Over-Scroll Mode

Control over-scroll behavior using the `OverScrollMode` enum:

- `OverScrollMode::Never` — Never allow over-scrolling
- `OverScrollMode::Always` — Always allow over-scrolling
- `OverScrollMode::ContentScrolls` — Allow over-scrolling only when content is scrollable

```cpp
scrollView.SetOverScrollMode(OverScrollMode::ContentScrolls);
```

Query the current mode:

```cpp
OverScrollMode mode = scrollView.GetOverScrollMode();
```

## Responding to Scroll Events

ScrollView provides signals for monitoring scroll and drag events. All scroll-related signals pass the source `ScrollView` to the callback.

### Scroll Signals

Connect to scroll lifecycle signals:

```cpp
class MyController : public ConnectionTracker
{
public:
    void SetupScrollView(ScrollView scrollView)
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
        // Handle continuous scrolling
    }

    void OnScrollFinished(ScrollView scrollView)
    {
        Vector2 pos = scrollView.GetScrollPosition();
        // Handle scroll end
    }
};
```

### Drag Signals

Monitor drag gestures separately from scroll animations:

```cpp
class MyController : public ConnectionTracker
{
public:
    void SetupDragSignals(ScrollView scrollView)
    {
        scrollView.DragStartedSignal().Connect(this, &MyController::OnDragStarted);
        scrollView.DraggingSignal().Connect(this, &MyController::OnDragging);
        scrollView.DragFinishedSignal().Connect(this, &MyController::OnDragFinished);
    }

    void OnDragStarted(ScrollView scrollView)
    {
        // User started a drag gesture
    }

    void OnDragging(ScrollView scrollView, float deltaX, float deltaY)
    {
        // User is dragging; deltaX and deltaY contain the drag delta
    }

    void OnDragFinished(ScrollView scrollView)
    {
        // User released after drag
    }
};
```

The `DraggingSignal()` callback receives the scroll delta values (`deltaX`, `deltaY`) in addition to the ScrollView instance.

## Complete Example

The following example demonstrates a fully configured ScrollView with content, scroll bars, and signal connections:

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

class ScrollExample : public ConnectionTracker
{
public:
    explicit ScrollExample(Application& application)
        : mApplication(application)
    {
        mApplication.InitSignal().Connect(this, &ScrollExample::Create);
    }

    void Create(Application application)
    {
        Window window = application.GetWindow();
        window.SetBackgroundColor(Color::WHITE);

        // Create scrollable content
        StackLayout content = StackLayout::New(StackOrientation::VERTICAL);
        content.SetRequestedWidth(MATCH_PARENT);
        content.SetRequestedHeight(WRAP_CONTENT);
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

        // Connect signals
        mScrollView.ScrollStartedSignal().Connect(this, &ScrollExample::OnScrollStarted);
        mScrollView.ScrollFinishedSignal().Connect(this, &ScrollExample::OnScrollFinished);

        window.Add(mScrollView);
    }

    void OnScrollStarted(ScrollView scrollView)
    {
        Vector2 pos = scrollView.GetScrollPosition();
    }

    void OnScrollFinished(ScrollView scrollView)
    {
        Vector2 pos = scrollView.GetScrollPosition();
    }

private:
    Application& mApplication;
    ScrollView   mScrollView;
};

int main(int argc, char** argv)
{
    Application application = Application::New(&argc, &argv);
    ScrollExample example(application);
    application.MainLoop();
    return 0;
}