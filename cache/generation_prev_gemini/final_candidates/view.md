---
title: View (Base UI Object)
sidebar_label: View (Base UI Object)
category: views-components
---

# View (Base UI Object)

`Dali::Ui::View` is the fundamental building block for all user interface components in the `dali-ui` framework, providing the core infrastructure for rendering, layout, and interaction.

## Overview

In `dali-ui`, every UI component inherits from `Dali::Ui::View`. Unlike the lower-level `Dali::Actor`, a `View` is designed specifically for application development, offering high-level properties for visual styling (such as backgrounds, borders, and rounded corners) and a trait-based system for handling complex behaviors like interaction and selection.

## Table of Contents

- [Creating and Configuring Views](#creating-and-configuring-views)
- [Visual Styling](#visual-styling)
- [Handling User Interaction](#handling-user-interaction)
- [Selection and Toggle States](#selection-and-toggle-states)
- [Animating View Properties](#animating-view-properties)
- [Layout and Geometry](#layout-and-geometry)

## Creating and Configuring Views

A `Dali::Ui::View` is instantiated using the `New()` method. Once created, it serves as a container or a leaf node in the UI hierarchy.

```cpp
using namespace Dali::Ui;

// Create a new View instance
View myView = View::New();

// Set an automation ID for testing or identification
myView.SetProperty(View::Property::AUTOMATION_ID, "main_content_view");

// Views can be named for easy debugging
myView.SetProperty(Dali::Actor::Property::NAME, "ContainerView");
```

## Visual Styling

`Dali::Ui::View` provides several built-in properties to customize its appearance without requiring custom shaders or complex manual rendering setup.

### Background and Color
The background can be set to a solid color or a more complex configuration.

```cpp
// Set a solid background color using the property system
myView.SetProperty(View::Property::BACKGROUND, Color::CYAN);

// Or use the explicit setter if available, or fetch it
UiColor bgColor = myView.GetBackgroundColor();
```

### Borders and Corners
You can easily add borders and rounded corners to any `View`.

```cpp
// Configure borderline
myView.SetProperty(View::Property::BORDERLINE_WIDTH, 2.0f);
myView.SetProperty(View::Property::BORDERLINE_COLOR, Color::BLACK);
myView.SetProperty(View::Property::BORDERLINE_OFFSET, 0.0f); // 0.0 is centered on the edge

// Apply rounded corners (top-left, top-right, bottom-right, bottom-left)
myView.SetProperty(View::Property::CORNER_RADIUS, Vector4(15.0f, 15.0f, 15.0f, 15.0f));

// Adjust corner squareness for more control over the curve shape
myView.SetProperty(View::Property::CORNER_SQUARENESS, Vector4(0.5f, 0.5f, 0.5f, 0.5f));
```

## Handling User Interaction

Interaction in `dali-ui` is managed via `Dali::Ui::InteractiveTrait`. This trait enables a `View` to respond to touch, mouse, and keyboard events.

To make a `View` interactive, you obtain its `InteractiveTrait` and connect to the desired signals.

```cpp
// Access the interactive capabilities of the View
InteractiveTrait interactive = myView.AsInteractive();

if(interactive)
{
  // Enable clickability
  interactive.SetClickable(true);
  
  // Connect to the clicked signal
  interactive.ConnectClickedSignal(this, &MyController::OnViewClicked);
  
  // Connect to pressed state changes (e.g., for visual feedback)
  interactive.ConnectPressedChangedSignal(this, &MyController::OnPressedChanged);
}

// Example callback signature
void MyController::OnViewClicked(View view, InputEvent event)
{
  // Handle the click event
}
```

## Selection and Toggle States

For components that need to maintain a selected or toggled state (like checkboxes or radio buttons), `Dali::Ui::SelectableTrait` is used.

```cpp
SelectableTrait selectable = SelectableTrait::New();
// Typically, traits are integrated into specific View subclasses, 
// but you can manage selection state through this trait.

selectable.SetSelected(true);
selectable.EnableToggleByClick(true);

// Monitor selection changes
selectable.SelectionChangedSignal().Connect(this, &MyController::OnSelectionChanged);

void MyController::OnSelectionChanged(View view, bool selected, InputEvent event)
{
  if(selected) { /* Handle selection */ }
}
```

## Animating View Properties

The `dali-ui` framework provides a high-level `Animate()` method on the `View` class, allowing for fluent animation of transform and visual properties.

```cpp
// Create an animation spec for a 500ms ease-in-out transition
ViewAnimationSpec spec = myView.NewAnimationSpec();
// (Configure spec duration/alpha here if symbols allow)

// Animate opacity and position
myView.Animate(Dali::Actor::Property::OPACITY, 1.0f);
myView.Animate(Dali::Actor::Property::POSITION, Vector3(100.0f, 200.0f, 0.0f));
```

## Layout and Geometry

`Dali::Ui::View` integrates with the layout system to determine its size and position relative to its parent.

- **Margins and Padding**: Use `View::Property::MARGIN` and `Dali::Actor::Property::PADDING` to control spacing.
- **Layout Mode**: Set `View::Property::LAYOUT_MODE` to define how the view behaves within a layout container.
- **Size Constraints**: Use `View::Property::MINIMUM_WIDTH`, `View::Property::MAXIMUM_WIDTH`, etc., to guide the layout engine.

```cpp
// Set external margins
myView.SetProperty(View::Property::MARGIN, Extents(10, 10, 5, 5));

// Set minimum and maximum dimensions
myView.SetProperty(View::Property::MINIMUM_HEIGHT, 50.0f);
myView.SetProperty(View::Property::MAXIMUM_WIDTH, 400.0f);

// Query measured size after layout pass
MeasuredSize size = myView.GetMeasuredSize();
```
