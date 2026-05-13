---
title: Scroll View
sidebar_label: Scroll View
category: views-components
---

# Scroll View

`Dali::Ui::ScrollView` is a `dali-ui` view container for panning content that is larger than the visible viewport.

## Table of Contents

- [Create a Scroll View Around Content](#create-a-scroll-view-around-content)
- [Choose the Scroll Direction](#choose-the-scroll-direction)
- [Set and Read the Scroll Position](#set-and-read-the-scroll-position)
- [Tune Fling and Over-Scroll Behavior](#tune-fling-and-over-scroll-behavior)
- [Control Scroll Bar Visibility](#control-scroll-bar-visibility)
- [React to Scroll and Drag Signals](#react-to-scroll-and-drag-signals)
- [Use `ScrollView` Handles Safely](#use-scrollview-handles-safely)
- [Fluent Chaining and Derived Components](#fluent-chaining-and-derived-components)

## Create a Scroll View Around Content

Create a scroll container with `Dali::Ui::ScrollView::New()`, then attach the content with `Dali::Ui::ScrollView::SetContent()`. The content is a `Dali::Ui::View`, so application code can keep the app-facing model in the `dali-ui` view tree instead of working directly with raw actor objects.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

ScrollView CreateVerticalScroller(View content)
{
  ScrollView scrollView = ScrollView::New()
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetContent(content);

  return scrollView;
}
```

`Dali::Ui::ScrollView::GetContent()` returns the current content view.

```cpp
void ReplaceScrollableContent(ScrollView scrollView, View newContent)
{
  View previousContent = scrollView.GetContent();

  scrollView
    .SetScrollPosition(Vector2(0.0f, 0.0f))
    .SetContent(newContent);
}
```

## Choose the Scroll Direction

Use `Dali::Ui::ScrollView::SetScrollDirection()` to constrain panning and fling movement. `ScrollDirection::Vertical` scrolls on the Y axis, `ScrollDirection::Horizontal` scrolls on the X axis, and `ScrollDirection::Both` enables two-dimensional scrolling.

```cpp
ScrollView CreateGalleryScroller(View content)
{
  return ScrollView::New()
    .SetScrollDirection(ScrollDirection::Horizontal)
    .SetContent(content);
}

ScrollView CreateCanvasScroller(View content)
{
  return ScrollView::New()
    .SetScrollDirection(ScrollDirection::Both)
    .SetContent(content);
}
```

Read the current mode with `Dali::Ui::ScrollView::GetScrollDirection()` when a component needs to adapt its behavior to the configured axis.

```cpp
bool UsesVerticalMotion(ScrollView scrollView)
{
  ScrollDirection direction = scrollView.GetScrollDirection();

  return direction == ScrollDirection::Vertical ||
         direction == ScrollDirection::Both;
}
```

## Set and Read the Scroll Position

`Dali::Ui::ScrollView::SetScrollPosition()` applies a scroll position without animation. For command-style movement, use `Dali::Ui::ScrollView::ScrollTo()`, `Dali::Ui::ScrollView::ScrollToX()`, or `Dali::Ui::ScrollView::ScrollToY()`. The scroll-to methods take an optional `bool animation` argument, which defaults to `true`.

```cpp
void ResetImmediately(ScrollView scrollView)
{
  scrollView.SetScrollPosition(Vector2(0.0f, 0.0f));
}

void ScrollToTopAnimated(ScrollView scrollView)
{
  scrollView.ScrollToY(0.0f);
}

void ScrollToPointWithoutAnimation(ScrollView scrollView)
{
  scrollView.ScrollTo(Vector2(240.0f, 480.0f), false);
}
```

`Dali::Ui::ScrollView::ScrollTo()` also has a child-view overload that scrolls a child into a requested position, using `ScrollToPosition::MakeVisible` by default.

```cpp
void RevealChild(ScrollView scrollView, View child)
{
  scrollView.ScrollTo(child);
}

void CenterChildWithoutAnimation(ScrollView scrollView, View child)
{
  scrollView.ScrollTo(child, false, ScrollToPosition::Center);
}
```

Use `Dali::Ui::ScrollView::GetScrollPosition()` to query the current offset, including from scroll signal callbacks.

```cpp
Vector2 ReadCurrentOffset(ScrollView scrollView)
{
  return scrollView.GetScrollPosition();
}

void MoveRightByPage(ScrollView scrollView, float pageWidth)
{
  Vector2 position = scrollView.GetScrollPosition();
  scrollView.ScrollToX(position.x + pageWidth);
}
```

`Dali::Ui::ScrollView::IsScrolling()` reports whether scrolling is currently in progress.

```cpp
void SnapOnlyWhenIdle(ScrollView scrollView, float snapY)
{
  if(!scrollView.IsScrolling())
  {
    scrollView.ScrollToY(snapY);
  }
}
```

## Tune Fling and Over-Scroll Behavior

Fling behavior is configured with typed setters on `Dali::Ui::ScrollView`. The setters return `ScrollView&`, so related configuration can be grouped in one fluent chain.

```cpp
ScrollView CreateTunedScroller(View content)
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

The matching getters let reusable components copy or inspect motion settings.

```cpp
void CopyFlingPolicy(ScrollView source, ScrollView target)
{
  target
    .SetMaxFlingDistance(source.GetMaxFlingDistance())
    .SetMinimumFlingDuration(source.GetMinimumFlingDuration())
    .SetMaximumFlingDuration(source.GetMaximumFlingDuration())
    .SetFlingSensitivity(source.GetFlingSensitivity())
    .SetDecelerationRate(source.GetDecelerationRate())
    .SetOverScrollMode(source.GetOverScrollMode());
}
```

`Dali::Ui::OverScrollMode` is the public policy value for over-scroll behavior. Use `OverScrollMode::Never` for no over-scrolling, `OverScrollMode::Always` to allow over-scrolling, or `OverScrollMode::ContentScrolls` to allow it only when the content is scrollable.

```cpp
void DisableEdgeOverScroll(ScrollView scrollView)
{
  scrollView.SetOverScrollMode(OverScrollMode::Never);
}

bool UsesContentDependentOverScroll(ScrollView scrollView)
{
  return scrollView.GetOverScrollMode() == OverScrollMode::ContentScrolls;
}
```

## Control Scroll Bar Visibility

Scroll bar visibility is controlled independently for each axis with `Dali::Ui::ScrollView::SetVerticalScrollBarVisibility()` and `Dali::Ui::ScrollView::SetHorizontalScrollBarVisibility()`.

```cpp
ScrollView CreateListScroller(View content)
{
  return ScrollView::New()
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
    .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never)
    .SetContent(content);
}

ScrollView CreateAlwaysVisibleTwoAxisScroller(View content)
{
  return ScrollView::New()
    .SetScrollDirection(ScrollDirection::Both)
    .SetVerticalScrollBarVisibility(ScrollBarVisibility::Always)
    .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Always)
    .SetContent(content);
}
```

Use `Dali::Ui::ScrollBarVisibility::Auto` to show the scroll bar while scrolling, `Dali::Ui::ScrollBarVisibility::Always` to keep it visible, and `Dali::Ui::ScrollBarVisibility::Never` to hide it. Read the configured state with `Dali::Ui::ScrollView::GetVerticalScrollBarVisibility()` and `Dali::Ui::ScrollView::GetHorizontalScrollBarVisibility()`.

```cpp
bool HasAnyVisibleScrollBarPolicy(ScrollView scrollView)
{
  return scrollView.GetVerticalScrollBarVisibility() != ScrollBarVisibility::Never ||
         scrollView.GetHorizontalScrollBarVisibility() != ScrollBarVisibility::Never;
}
```

## React to Scroll and Drag Signals

`Dali::Ui::ScrollView` exposes scroll-session signals and drag-specific signals. `Dali::Ui::ScrollView::ScrollStartedSignal()`, `Dali::Ui::ScrollView::ScrollingSignal()`, and `Dali::Ui::ScrollView::ScrollFinishedSignal()` use callbacks with the signature `void(Dali::Ui::ScrollView)`.

```cpp
class ScrollStatus : public Dali::ConnectionTracker
{
public:
  ScrollStatus()
  : Dali::ConnectionTracker()
  {
  }

  void Attach(ScrollView scrollView)
  {
    scrollView.ScrollStartedSignal().Connect(this, &ScrollStatus::OnScrollStarted);
    scrollView.ScrollingSignal().Connect(this, &ScrollStatus::OnScrolling);
    scrollView.ScrollFinishedSignal().Connect(this, &ScrollStatus::OnScrollFinished);
  }

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

`Dali::Ui::ScrollView::DragStartedSignal()` and `Dali::Ui::ScrollView::DragFinishedSignal()` also use `void(Dali::Ui::ScrollView)`. `Dali::Ui::ScrollView::DraggingSignal()` uses `void(Dali::Ui::ScrollView, float, float)`, where the two `float` values are the drag delta values supplied by the signal.

```cpp
class DragObserver : public Dali::ConnectionTracker
{
public:
  DragObserver()
  : Dali::ConnectionTracker()
  {
  }

  void Attach(ScrollView scrollView)
  {
    scrollView.DragStartedSignal().Connect(this, &DragObserver::OnDragStarted);
    scrollView.DraggingSignal().Connect(this, &DragObserver::OnDragging);
    scrollView.DragFinishedSignal().Connect(this, &DragObserver::OnDragFinished);
  }

  void OnDragStarted(ScrollView scrollView)
  {
    Vector2 position = scrollView.GetScrollPosition();
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

## Use `ScrollView` Handles Safely

A default-constructed `Dali::Ui::ScrollView` is an uninitialized handle. Use `Dali::Ui::ScrollView::New()` when creating a usable scroll view.

```cpp
ScrollView CreateInitializedScrollView(View content)
{
  ScrollView scrollView = ScrollView::New();
  scrollView.SetContent(content);
  return scrollView;
}
```

Use `Dali::Ui::ScrollView::DownCast()` when a component receives a generic handle and needs to work with it as a `ScrollView`.

```cpp
ScrollView AsScrollView(BaseHandle handle)
{
  return ScrollView::DownCast(handle);
}
```

`Dali::Ui::ScrollView` supports move assignment through `Dali::Ui::ScrollView::operator=`, so a handle value can be moved between variables.

```cpp
void StoreScroller(ScrollView& target, ScrollView replacement)
{
  target = std::move(replacement);
}
```

## Fluent Chaining and Derived Components

`Dali::Ui::ScrollView` setters such as `Dali::Ui::ScrollView::SetContent()`, `Dali::Ui::ScrollView::SetScrollDirection()`, `Dali::Ui::ScrollView::SetOverScrollMode()`, and the scroll bar visibility setters return `ScrollView&`. This supports compact, typed setup without raw property access.

```cpp
ScrollView CreateConfiguredScroller(View content)
{
  return ScrollView::New()
    .SetContent(content)
    .SetScrollPosition(Vector2(0.0f, 0.0f))
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetOverScrollMode(OverScrollMode::ContentScrolls)
    .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
    .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);
}
```

The generated header `scroll-view.autogen.h` provides `DALI_UI_CHAIN_SCROLLVIEW_METHODS` for dali-ui components derived from `Dali::Ui::ScrollView`. Application developers usually consume the result as normal fluent setters on `ScrollView`; component authors can use the macro so derived classes keep returning the derived type from inherited scroll-view setters.

```cpp
class FeedScrollView : public ScrollView
{
public:
  DALI_UI_CHAIN_SCROLLVIEW_METHODS(FeedScrollView)
};
```
