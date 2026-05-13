---
title: Scroll View
sidebar_label: Scroll View
category: views-components
---

# Scroll View

`Dali::Ui::ScrollView` is a dali-ui `Dali::Ui::View` container for panning content that is larger than its visible viewport.

## Table of Contents

- [Create a Scroll View Around Content](#create-a-scroll-view-around-content)
- [Choose the Scroll Axis and Edge Behavior](#choose-the-scroll-axis-and-edge-behavior)
- [Tune Fling and Deceleration](#tune-fling-and-deceleration)
- [Show Scroll Bars](#show-scroll-bars)
- [Control and Read the Scroll Position](#control-and-read-the-scroll-position)
- [Respond to Scroll and Drag Signals](#respond-to-scroll-and-drag-signals)
- [Use Fluent Scroll View APIs](#use-fluent-scroll-view-apis)

## Create a Scroll View Around Content

Create a `Dali::Ui::ScrollView` with `Dali::Ui::ScrollView::New`, then attach the view subtree to be scrolled with `Dali::Ui::ScrollView::SetContent`. The content is a `Dali::Ui::View`, so application code should build the scrollable area as a dali-ui view tree and pass that root view to the scroll view.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::ScrollView CreateVerticalScrollArea(Dali::Ui::View content)
{
  Dali::Ui::ScrollView scrollView = Dali::Ui::ScrollView::New()
    .SetScrollDirection(Dali::Ui::ScrollDirection::Vertical)
    .SetContent(content);

  return scrollView;
}
```

Use `Dali::Ui::ScrollView::GetContent` when later code needs the content root that was assigned to the scroll view.

```cpp
void ReplaceContent(Dali::Ui::ScrollView scrollView, Dali::Ui::View newContent)
{
  Dali::Ui::View previousContent = scrollView.GetContent();

  scrollView.SetContent(newContent);
}
```

## Choose the Scroll Axis and Edge Behavior

`Dali::Ui::ScrollView::SetScrollDirection` controls whether the scroll view responds vertically, horizontally, or in both directions. Use `Dali::Ui::ScrollDirection::Vertical` for lists, `Dali::Ui::ScrollDirection::Horizontal` for sideways strips, and `Dali::Ui::ScrollDirection::Both` when the content can move on both axes.

`Dali::Ui::ScrollView::SetOverScrollMode` controls how edge over-scroll is allowed. `Dali::Ui::OverScrollMode::Never` disables it, `Dali::Ui::OverScrollMode::Always` allows it, and `Dali::Ui::OverScrollMode::ContentScrolls` allows it when the content is scrollable.

```cpp
Dali::Ui::ScrollView CreateTwoAxisCanvas(Dali::Ui::View content)
{
  return Dali::Ui::ScrollView::New()
    .SetScrollDirection(Dali::Ui::ScrollDirection::Both)
    .SetOverScrollMode(Dali::Ui::OverScrollMode::ContentScrolls)
    .SetContent(content);
}
```

Read back the current configuration with `Dali::Ui::ScrollView::GetScrollDirection` and `Dali::Ui::ScrollView::GetOverScrollMode`.

```cpp
bool UsesTwoAxisScrolling(Dali::Ui::ScrollView scrollView)
{
  return scrollView.GetScrollDirection() == Dali::Ui::ScrollDirection::Both;
}
```

## Tune Fling and Deceleration

`Dali::Ui::ScrollView` exposes typed setters for fling behavior. `Dali::Ui::ScrollView::SetMaxFlingDistance` limits how far one fling can move. `Dali::Ui::ScrollView::SetMinimumFlingDuration` and `Dali::Ui::ScrollView::SetMaximumFlingDuration` bound the fling animation duration. `Dali::Ui::ScrollView::SetFlingSensitivity` adjusts fling sensitivity, and `Dali::Ui::ScrollView::SetDecelerationRate` sets the rate used while fling motion slows down.

```cpp
Dali::Ui::ScrollView CreateTunedList(Dali::Ui::View content)
{
  return Dali::Ui::ScrollView::New()
    .SetScrollDirection(Dali::Ui::ScrollDirection::Vertical)
    .SetMaxFlingDistance(6000.0f)
    .SetMinimumFlingDuration(1000)
    .SetMaximumFlingDuration(2000)
    .SetFlingSensitivity(1.0f)
    .SetDecelerationRate(0.998f)
    .SetContent(content);
}
```

Use the matching getters when you need to preserve or compare current behavior.

```cpp
void CopyFlingTuning(Dali::Ui::ScrollView source, Dali::Ui::ScrollView target)
{
  target
    .SetMaxFlingDistance(source.GetMaxFlingDistance())
    .SetMinimumFlingDuration(source.GetMinimumFlingDuration())
    .SetMaximumFlingDuration(source.GetMaximumFlingDuration())
    .SetFlingSensitivity(source.GetFlingSensitivity())
    .SetDecelerationRate(source.GetDecelerationRate());
}
```

## Show Scroll Bars

`Dali::Ui::ScrollView` owns scroll bar visibility settings directly. Use `Dali::Ui::ScrollView::SetVerticalScrollBarVisibility` and `Dali::Ui::ScrollView::SetHorizontalScrollBarVisibility` instead of creating a separate `Dali::Ui::ScrollBar` for the common scroll view case.

`Dali::Ui::ScrollBarVisibility::Auto` shows the bar only while scrolling, `Dali::Ui::ScrollBarVisibility::Always` keeps it visible, and `Dali::Ui::ScrollBarVisibility::Never` hides it.

```cpp
Dali::Ui::ScrollView CreateListWithVerticalScrollBar(Dali::Ui::View content)
{
  return Dali::Ui::ScrollView::New()
    .SetScrollDirection(Dali::Ui::ScrollDirection::Vertical)
    .SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto)
    .SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never)
    .SetContent(content);
}
```

The visibility state can be queried with `Dali::Ui::ScrollView::GetVerticalScrollBarVisibility` and `Dali::Ui::ScrollView::GetHorizontalScrollBarVisibility`.

```cpp
bool ShowsVerticalScrollFeedback(Dali::Ui::ScrollView scrollView)
{
  return scrollView.GetVerticalScrollBarVisibility() != Dali::Ui::ScrollBarVisibility::Never;
}
```

## Control and Read the Scroll Position

Use `Dali::Ui::ScrollView::SetScrollPosition` to set the current position directly. Use `Dali::Ui::ScrollView::ScrollTo`, `Dali::Ui::ScrollView::ScrollToX`, and `Dali::Ui::ScrollView::ScrollToY` when you want the scroll view to move to a target position, optionally with animation.

```cpp
void JumpToTop(Dali::Ui::ScrollView scrollView)
{
  scrollView.ScrollToY(0.0f, false);
}
```

```cpp
void MoveToDocumentPosition(Dali::Ui::ScrollView scrollView, const Dali::Vector2& position)
{
  scrollView.ScrollTo(position, true);
}
```

`Dali::Ui::ScrollView::GetScrollPosition` returns the current `Dali::Vector2` scroll position, and `Dali::Ui::ScrollView::IsScrolling` reports whether scrolling is in progress.

```cpp
Dali::Vector2 GetPositionIfIdle(Dali::Ui::ScrollView scrollView)
{
  if(scrollView.IsScrolling())
  {
    return scrollView.GetScrollPosition();
  }

  return scrollView.GetScrollPosition();
}
```

## Respond to Scroll and Drag Signals

Connect to `Dali::Ui::ScrollView::ScrollStartedSignal`, `Dali::Ui::ScrollView::ScrollingSignal`, and `Dali::Ui::ScrollView::ScrollFinishedSignal` for scroll lifecycle updates. Each scroll signal uses a callback that receives the `Dali::Ui::ScrollView`.

```cpp
class ScrollObserver : public Dali::ConnectionTracker
{
public:
  ScrollObserver() = default;

  void Attach(Dali::Ui::ScrollView scrollView)
  {
    scrollView.ScrollStartedSignal().Connect(this, &ScrollObserver::OnScrollStarted);
    scrollView.ScrollingSignal().Connect(this, &ScrollObserver::OnScrolling);
    scrollView.ScrollFinishedSignal().Connect(this, &ScrollObserver::OnScrollFinished);
  }

private:
  void OnScrollStarted(Dali::Ui::ScrollView scrollView)
  {
    Dali::Vector2 position = scrollView.GetScrollPosition();
  }

  void OnScrolling(Dali::Ui::ScrollView scrollView)
  {
    Dali::Vector2 position = scrollView.GetScrollPosition();
  }

  void OnScrollFinished(Dali::Ui::ScrollView scrollView)
  {
    Dali::Vector2 position = scrollView.GetScrollPosition();
  }
};
```

Use `Dali::Ui::ScrollView::DragStartedSignal`, `Dali::Ui::ScrollView::DraggingSignal`, and `Dali::Ui::ScrollView::DragFinishedSignal` when you need drag-specific callbacks. `Dali::Ui::ScrollView::DraggingSignal` provides the scroll view plus `deltaX` and `deltaY`.

```cpp
class DragObserver : public Dali::ConnectionTracker
{
public:
  DragObserver() = default;

  void Attach(Dali::Ui::ScrollView scrollView)
  {
    scrollView.DragStartedSignal().Connect(this, &DragObserver::OnDragStarted);
    scrollView.DraggingSignal().Connect(this, &DragObserver::OnDragging);
    scrollView.DragFinishedSignal().Connect(this, &DragObserver::OnDragFinished);
  }

private:
  void OnDragStarted(Dali::Ui::ScrollView scrollView)
  {
    Dali::Vector2 position = scrollView.GetScrollPosition();
  }

  void OnDragging(Dali::Ui::ScrollView scrollView, float deltaX, float deltaY)
  {
    Dali::Vector2 position = scrollView.GetScrollPosition();
  }

  void OnDragFinished(Dali::Ui::ScrollView scrollView)
  {
    Dali::Vector2 position = scrollView.GetScrollPosition();
  }
};
```

## Use Fluent Scroll View APIs

Most application code uses the fluent setters provided by `Dali::Ui::ScrollView`, such as `Dali::Ui::ScrollView::SetContent`, `Dali::Ui::ScrollView::SetScrollDirection`, and `Dali::Ui::ScrollView::SetOverScrollMode`. These setters return `Dali::Ui::ScrollView&`, so configuration can stay close to creation.

```cpp
Dali::Ui::ScrollView CreateConfiguredScrollView(Dali::Ui::View content)
{
  Dali::Ui::ScrollView scrollView = Dali::Ui::ScrollView::New()
    .SetScrollDirection(Dali::Ui::ScrollDirection::Vertical)
    .SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto)
    .SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never)
    .SetOverScrollMode(Dali::Ui::OverScrollMode::ContentScrolls)
    .SetContent(content);

  return scrollView;
}
```

The generated `scroll-view.autogen.h` header defines `DALI_UI_CHAIN_SCROLLVIEW_METHODS` for classes that extend the scroll view handle pattern. Application developers normally use the resulting chainable methods through `Dali::Ui::ScrollView` rather than including the generated header directly.

```cpp
class CustomScrollView : public Dali::Ui::ScrollView
{
public:
  DALI_UI_CHAIN_SCROLLVIEW_METHODS(CustomScrollView)
};
```
