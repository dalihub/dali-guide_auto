---
title: Ui Scale Manager
sidebar_label: Ui Scale Manager
category: styling-theme-config
---

# Ui Scale Manager

UiScaleManager provides access to the system-driven adaptive UI scale, allowing applications to respond to display scale changes and control how individual views participate in scaling.

## Table of Contents

- [Getting the Current Scale](#getting-the-current-scale)
- [Setting the System Scale](#setting-the-system-scale)
- [View Scale Policies](#view-scale-policies)
- [Practical Examples](#practical-examples)

## Getting the Current Scale

The `UiScaleManager` is a singleton that exposes the current system scale factor. Use `UiScaleManager::Get()` to obtain the singleton instance, then call `GetScale()` to retrieve the current scale value.

```cpp
// Get the current system scale factor
float scale = Dali::Ui::UiScaleManager::Get().GetScale();

// Default scale is 1.0
if(scale > 1.0f)
{
  // Handle enlarged UI mode
}
```

The scale value defaults to 1.0 and can be read at any time during application execution.

## Setting the System Scale

Use `SetScale()` to change the system scale factor. This triggers re-layout of all registered layout roots.

```cpp
// Set scale to 1.5x (150%)
Dali::Ui::UiScaleManager::Get().SetScale(1.5f);

// Set scale to 2.0x (200%)
Dali::Ui::UiScaleManager::Get().SetScale(2.0f);
```

When the scale changes, all views with the appropriate policy will be resized proportionally. Typical scale values range from 0.8 to 2.0.

## View Scale Policies

Each view can control how it participates in system-driven scaling through `UiScalePolicy`. Set the policy using `SetUiScalePolicy()` on any view.

### Available Policies

| Policy | Behavior |
|--------|----------|
| `UiScalePolicy::INHERIT` | Inherits effective scale from parent; root views inherit from UiScaleManager. This is the default. |
| `UiScalePolicy::ENABLED` | Always applies the current UiScaleManager scale, ignoring parent policy. |
| `UiScalePolicy::DISABLED` | Opts out of scaling (effectiveScale = 1.0); descendant INHERIT views also get 1.0. |

### Setting Policy on Views

```cpp
using namespace Dali::Ui;

// Create a view with default INHERIT policy
View scaledView = View::New();
scaledView.SetUiScalePolicy(UiScalePolicy::INHERIT);  // default

// Create a view that always scales with the system
View alwaysScaledView = View::New();
alwaysScaledView.SetUiScalePolicy(UiScalePolicy::ENABLED);

// Create a view that never scales
View fixedSizeView = View::New();
fixedSizeView.SetUiScalePolicy(UiScalePolicy::DISABLED);
```

### Policy Inheritance

The policy system follows a hierarchical model:

- **INHERIT (default)**: The view inherits its effective scale from its parent. Root views inherit directly from `UiScaleManager`.
- **ENABLED**: The view bypasses its parent's policy and always uses the system scale directly. Use this when a child should scale even inside a non-scaling container.
- **DISABLED**: The view opts out of scaling entirely, creating a "scale-free island." Descendants with INHERIT will also have effectiveScale = 1.0, but descendants with ENABLED can still scale.

## Practical Examples

### Scale Control Panel

This example shows a complete scale control implementation with preset buttons and custom input:

```cpp
class ScaleController : public Dali::ConnectionTracker
{
public:
  void ApplyScale(float scale)
  {
    Dali::Ui::UiScaleManager::Get().SetScale(scale);
    UpdateScaleLabel();
  }

  void UpdateScaleLabel()
  {
    float currentScale = Dali::Ui::UiScaleManager::Get().GetScale();
    mScaleLabel.SetText("System Scale: " + std::to_string(currentScale));
  }

private:
  Dali::Ui::Label mScaleLabel;
};
```

### Mixed Policy Layout

This example demonstrates combining different policies in a single layout:

```cpp
using namespace Dali::Ui;

// Container that does NOT scale
StackLayout fixedContainer = StackLayout::New(StackOrientation::HORIZONTAL);
fixedContainer.SetUiScalePolicy(UiScalePolicy::DISABLED);

// Child that inherits DISABLED (also won't scale)
View fixedChild = View::New();
fixedChild.SetUiScalePolicy(UiScalePolicy::INHERIT);  // inherits 1.0 from parent
fixedContainer.Add(fixedChild);

// Child that scales despite DISABLED parent
View scalingChild = View::New();
scalingChild.SetUiScalePolicy(UiScalePolicy::ENABLED);  // uses system scale directly
fixedContainer.Add(scalingChild);
```

### Nested Scale Islands

Create isolated scale-free regions within a scaled layout:

```cpp
// Main layout scales with system
StackLayout mainLayout = StackLayout::New(StackOrientation::VERTICAL);
// INHERIT is default - scales with system

// Create a scale-free island
StackLayout scaleFreeZone = StackLayout::New(StackOrientation::HORIZONTAL);
scaleFreeZone.SetUiScalePolicy(UiScalePolicy::DISABLED);
// All INHERIT children inside stay at 1.0 scale

// ENABLED child breaks out of the island
View scalingButton = View::New();
scalingButton.SetUiScalePolicy(UiScalePolicy::ENABLED);
scaleFreeZone.Add(scalingButton);  // This child still scales

mainLayout.Add(scaleFreeZone);
```

### Reading Scale for Conditional Logic

```cpp
float scale = Dali::Ui::UiScaleManager::Get().GetScale();

if(scale >= 1.5f)
{
  // Use compact layout for large scale
  UseCompactLayout();
}
else
{
  // Use standard layout
  UseStandardLayout();
}
```