---
title: "Scroll View"
sidebar_label: "Scroll View"
category: "views-components"
---

# Scroll View

`Dali::Ui::ScrollView` is a dali-ui `Dali::Ui::View` container for presenting content that is larger than its visible viewport.

## Table of Contents

- [Create a Scroll View](#create-a-scroll-view)
- [Choose Scroll Direction and Bars](#choose-scroll-direction-and-bars)
- [Control Position Programmatically](#control-position-programmatically)
- [Tune Fling and Over-scroll Behavior](#tune-fling-and-over-scroll-behavior)
- [React to Scroll and Drag Signals](#react-to-scroll-and-drag-signals)
- [Use the Fluent ScrollView Surface](#use-the-fluent-scrollview-surface)

## Create a Scroll View

Create `Dali::Ui::ScrollView` with `Dali::Ui::ScrollView::New`, then attach a content `Dali::Ui::View` with `Dali::Ui::ScrollView::SetContent`. The content view is the view tree that moves inside the scroll viewport.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

ScrollView CreateVerticalScrollView(View content)
{
  ScrollView scrollView = ScrollView::New();

  scrollView
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetContent(content);

  return scrollView;
}
```

`Dali::Ui::ScrollView::GetContent` returns the current content `Dali::Ui::View`, which is useful when the app needs to inspect or replace the scrollable view tree.

```cpp
void ReplaceScrollContent(ScrollView scrollView, View nextContent)
{
  View previousContent = scrollView.GetContent();

  scrollView.SetContent(nextContent);
}
```

If you receive a generic DALi handle from another part of the app, use `Dali::Ui::ScrollView::DownCast` before calling scroll-specific APIs.

```cpp
void ConfigureIfScrollView(BaseHandle handle, View content)
{
  ScrollView scrollView = ScrollView::DownCast(handle);

  if(scrollView)
  {
    scrollView
      .SetScrollDirection(ScrollDirection::Vertical)
      .SetContent(content);
  }
}
```

## Choose Scroll Direction and Bars

`Dali::Ui::ScrollView::SetScrollDirection` accepts `Dali::Ui::ScrollDirection::Vertical`, `Dali::Ui::ScrollDirection::Horizontal`, or `Dali::Ui::ScrollDirection::Both`.

Use `Dali::Ui::ScrollView::SetVerticalScrollBarVisibility` and `Dali::Ui::ScrollView::SetHorizontalScrollBarVisibility` to control the built-in bar visibility. `Dali::Ui::ScrollBarVisibility::Auto` shows the bar as needed, `Dali::Ui::ScrollBarVisibility::Always` keeps it visible, and `Dali::Ui::ScrollBarVisibility::Never` hides it.

```cpp
ScrollView CreateListScrollView(View listContent)
{
  return ScrollView::New()
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
    .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never)
    .SetContent(listContent);
}
```

For horizontally scrollable content, swap the direction and bar policy.

```cpp
ScrollView CreateHorizontalScrollView(View rowContent)
{
  return ScrollView::New()
    .SetScrollDirection(ScrollDirection::Horizontal)
    .SetVerticalScrollBarVisibility(ScrollBarVisibility::Never)
    .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Auto)
    .SetContent(rowContent);
}
```

You can read back the configuration with `Dali::Ui::ScrollView::GetScrollDirection`, `Dali::Ui::ScrollView::GetVerticalScrollBarVisibility`, and `Dali::Ui::ScrollView::GetHorizontalScrollBarVisibility`.

```cpp
bool UsesVerticalAutoBar(ScrollView scrollView)
{
  return scrollView.GetScrollDirection() == ScrollDirection::Vertical &&
         scrollView.GetVerticalScrollBarVisibility() == ScrollBarVisibility::Auto;
}
```

## Control Position Programmatically

Use `Dali::Ui::ScrollView::SetScrollPosition` to set the stored scroll position immediately. Use `Dali::Ui::ScrollView::ScrollTo`, `Dali::Ui::ScrollView::ScrollToX`, or `Dali::Ui::ScrollView::ScrollToY` when you want the scroll view to move to a target position, optionally with animation.

```cpp
void RestoreScrollPosition(ScrollView scrollView, const Vector2& savedPosition)
{
  scrollView.SetScrollPosition(savedPosition);
}
```

```cpp
void MoveToTop(ScrollView scrollView)
{
  scrollView.ScrollToY(0.0f, true);
}
```

```cpp
void MoveToPositionWithoutAnimation(ScrollView scrollView)
{
  scrollView.ScrollTo(Vector2(0.0f, 480.0f), false);
}
```

Use `Dali::Ui::ScrollView::GetScrollPosition` to store the current position or update nearby UI.

```cpp
Vector2 SaveScrollPosition(ScrollView scrollView)
{
  return scrollView.GetScrollPosition();
}
```

For a two-axis viewport, call `Dali::Ui::ScrollView::SetScrollDirection` with `Dali::Ui::ScrollDirection::Both`, then use `Dali::Ui::ScrollView::ScrollTo` with a `Vector2`.

```cpp
ScrollView CreateTwoAxisScrollView(View canvasContent)
{
  ScrollView scrollView = ScrollView::New()
    .SetScrollDirection(ScrollDirection::Both)
    .SetContent(canvasContent);

  scrollView.ScrollTo(Vector2(320.0f, 240.0f), true);

  return scrollView;
}
```

## Tune Fling and Over-scroll Behavior

`Dali::Ui::ScrollView` exposes typed setters for fling distance, fling duration, fling sensitivity, deceleration, and over-scroll mode. These are regular `ScrollView` methods, so they can be chained during setup.

```cpp
ScrollView CreateTunedScrollView(View content)
{
  return ScrollView::New()
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetMaxFlingDistance(6000.0f)
    .SetMinimumFlingDuration(1000)
    .SetMaximumFlingDuration(2000)
    .SetFlingSensitivity(1.0f)
    .SetDecelerationRate(0.998f)
    .SetOverScrollMode(OverScrollMode::ContentScrolls)
    .SetContent(content);
}
```

`Dali::Ui::OverScrollMode::Never` disables over-scrolling, `Dali::Ui::OverScrollMode::Always` allows it, and `Dali::Ui::OverScrollMode::ContentScrolls` allows it when the content can scroll.

```cpp
void DisableOverScroll(ScrollView scrollView)
{
  scrollView.SetOverScrollMode(OverScrollMode::Never);
}
```

Use the matching getters when you need to preserve a user or app profile.

```cpp
struct ScrollTuning
{
  float decelerationRate;
  float flingSensitivity;
  float maxFlingDistance;
  int minimumFlingDuration;
  int maximumFlingDuration;
  OverScrollMode overScrollMode;
};

ScrollTuning ReadScrollTuning(ScrollView scrollView)
{
  return ScrollTuning{
    scrollView.GetDecelerationRate(),
    scrollView.GetFlingSensitivity(),
    scrollView.GetMaxFlingDistance(),
    scrollView.GetMinimumFlingDuration(),
    scrollView.GetMaximumFlingDuration(),
    scrollView.GetOverScrollMode()};
}
```

## React to Scroll and Drag Signals

Connect to the `Dali::Ui::ScrollView` signals from a class that owns a `Dali::ConnectionTracker`. `Dali::Ui::ScrollView::ScrollStartedSignal`, `Dali::Ui::ScrollView::ScrollingSignal`, and `Dali::Ui::ScrollView::ScrollFinishedSignal` use callbacks with one `Dali::Ui::ScrollView` parameter.

```cpp
class ScrollObserver : public ConnectionTracker
{
public:
  ScrollObserver()
  : ConnectionTracker()
  {
  }

  void Attach(ScrollView scrollView)
  {
    scrollView.ScrollStartedSignal().Connect(this, &ScrollObserver::OnScrollStarted);
    scrollView.ScrollingSignal().Connect(this, &ScrollObserver::OnScrolling);
    scrollView.ScrollFinishedSignal().Connect(this, &ScrollObserver::OnScrollFinished);
  }

private:
  void OnScrollStarted(ScrollView scrollView)
  {
    Vector2 position = scrollView.GetScrollPosition();
  }

  void OnScrolling(ScrollView scrollView)
  {
    Vector2 position = scrollView.GetScrollPosition();
  }

  void OnScrollFinished(ScrollView scrollView)
  {
    Vector2 position = scrollView.GetScrollPosition();
  }
};
```

`Dali::Ui::ScrollView::DragStartedSignal` and `Dali::Ui::ScrollView::DragFinishedSignal` also use one `Dali::Ui::ScrollView` parameter. `Dali::Ui::ScrollView::DraggingSignal` provides the scroll view plus `deltaX` and `deltaY`.

```cpp
class DragObserver : public ConnectionTracker
{
public:
  DragObserver()
  : ConnectionTracker()
  {
  }

  void Attach(ScrollView scrollView)
  {
    scrollView.DragStartedSignal().Connect(this, &DragObserver::OnDragStarted);
    scrollView.DraggingSignal().Connect(this, &DragObserver::OnDragging);
    scrollView.DragFinishedSignal().Connect(this, &DragObserver::OnDragFinished);
  }

private:
  void OnDragStarted(ScrollView scrollView)
  {
    bool scrolling = scrollView.IsScrolling();
  }

  void OnDragging(ScrollView scrollView, float deltaX, float deltaY)
  {
    Vector2 position = scrollView.GetScrollPosition();
  }

  void OnDragFinished(ScrollView scrollView)
  {
    Vector2 position = scrollView.GetScrollPosition();
  }
};
```

## Use the Fluent ScrollView Surface

Most configuration setters on `Dali::Ui::ScrollView` return `Dali::Ui::ScrollView&`, so app code can use a compact fluent style with typed dali-ui APIs.

```cpp
ScrollView ConfigureScrollView(ScrollView scrollView, View content)
{
  scrollView
    .SetContent(content)
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetScrollPosition(Vector2(0.0f, 0.0f))
    .SetOverScrollMode(OverScrollMode::ContentScrolls)
    .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
    .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);

  return scrollView;
}
```

`scroll-view.autogen.h` is part of the public `Dali::Ui::ScrollView` feature surface. Its `DALI_UI_CHAIN_SCROLLVIEW_METHODS` macro provides chainable forwarding methods for classes that derive from `Dali::Ui::ScrollView`, while preserving the same typed setter names such as `SetContent`, `SetScrollDirection`, and `SetOverScrollMode`.

Application code normally uses the generated fluent methods through `Dali::Ui::ScrollView` itself.

```cpp
ScrollView scrollView = ScrollView::New()
  .SetScrollDirection(ScrollDirection::Vertical)
  .SetMaxFlingDistance(6000.0f)
  .SetMinimumFlingDuration(1000)
  .SetMaximumFlingDuration(2000)
  .SetFlingSensitivity(1.0f)
  .SetDecelerationRate(0.998f)
  .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
  .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);
```
