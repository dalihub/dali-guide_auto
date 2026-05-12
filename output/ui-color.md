---
title: Ui Color
sidebar_label: Ui Color
category: uncategorized
---

# Ui Color

`UiColor` provides a theme-aware color value that can hold either direct RGBA components or a string identifier resolved at runtime from the current theme.

## Table of Contents

- [Creating UiColor Values](#creating-uicolor-values)
- [Theme Color Identifiers](#theme-color-identifiers)
- [Alpha Manipulation](#alpha-manipulation)
- [Retrieving Color Values](#retrieving-color-values)

## Creating UiColor Values

`UiColor` supports multiple construction patterns for different use cases.

### Direct RGBA Values

Create a `UiColor` with explicit red, green, blue, and alpha components:

```cpp
// Full RGBA constructor
UiColor red(1.0f, 0.0f, 0.0f, 1.0f);

// RGB with default alpha (1.0)
UiColor gray(0.5f, 0.5f, 0.5f);
```

### From Vector4

Construct a `UiColor` from an existing `Vector4`:

```cpp
Vector4 rgba(0.2f, 0.4f, 0.6f, 0.8f);
UiColor color(rgba);
```

### From Hex RGB

Use hex notation for web-style color definitions:

```cpp
// 0xRRGGBB format, alpha defaults to 1.0
UiColor coral(0xFF7F50u);

// With explicit alpha
UiColor translucentGreen(0x00FF00u, 0.5f);
```

### Default Construction

A default-constructed `UiColor` is transparent black:

```cpp
UiColor transparent;  // RGBA (0, 0, 0, 0)
```

## Theme Color Identifiers

`UiColor` can reference colors by string identifier, resolved from the current theme at runtime.

### String-Based Color IDs

```cpp
// Create from color ID string
UiColor themeColor("ThemeColor1");

// Move semantics for efficiency
UiColor movedColor(String("ThemeColor2"));
```

When `GetRgba()` is called, the actual RGBA value is looked up from the current theme. If the ID is not found, the fallback is transparent black.

### Predefined Color Constants

`UiColor` provides static constants for common theme colors:

| Constant | Color ID |
|----------|----------|
| `UiColor::PRIMARY` | "Primary" |
| `UiColor::BACKGROUND` | "Background" |
| `UiColor::OUTLINE` | "Outline" |

```cpp
// Use predefined theme colors
view.SetBackgroundColor(UiColor::PRIMARY);
layout.SetBackgroundColor(UiColor::BACKGROUND);
```

### Checking Color ID Presence

Use `HasColorId()` to determine if a `UiColor` holds a string identifier:

```cpp
UiColor direct(1.0f, 0.0f, 0.0f, 1.0f);
UiColor themed("Primary");

direct.HasColorId();   // false
themed.HasColorId();   // true
```

Retrieve the identifier with `GetColorId()`:

```cpp
UiColor color("MyColorId");
String id = color.GetColorId();  // "MyColorId"

UiColor rgba(1.0f, 0.0f, 0.0f, 1.0f);
rgba.GetColorId();  // Returns empty string
```

## Alpha Manipulation

`UiColor` provides fluent methods for alpha modification that return new instances without modifying the original.

### WithAlpha

Replace the alpha component with a specific value:

```cpp
UiColor red(1.0f, 0.0f, 0.0f, 1.0f);
UiColor translucent = red.WithAlpha(0.3f);

// Values are clamped to [0.0, 1.0]
UiColor clamped = red.WithAlpha(-0.5f);  // alpha becomes 0.0
UiColor clamped2 = red.WithAlpha(2.0f);  // alpha becomes 1.0
```

For theme-based colors, `WithAlpha()` preserves the color ID:

```cpp
UiColor primary = UiColor::PRIMARY.WithAlpha(0.7f);
primary.HasColorId();  // true
primary.GetColorId();   // "Primary"
```

### ScaleAlpha

Multiply the alpha by a scaling factor:

```cpp
UiColor color(1.0f, 0.0f, 0.0f, 0.8f);
UiColor result = color.ScaleAlpha(0.5f);
// result.alpha == 0.4
```

The result is clamped to valid range:

```cpp
UiColor color(1.0f, 0.0f, 0.0f, 1.0f);
UiColor scaled = color.ScaleAlpha(2.0f);
// scaled.alpha clamped to 1.0
```

### Method Chaining

Both alpha methods can be chained for compound operations:

```cpp
// WithAlpha then ScaleAlpha: 0.5 * 0.2 = 0.1
UiColor result = UiColor::PRIMARY.WithAlpha(0.5f).ScaleAlpha(0.2f);

// Multiple ScaleAlpha calls multiply: 1.0 * 0.5 * 0.2 = 0.1
UiColor scaled = UiColor::PRIMARY.ScaleAlpha(0.5f).ScaleAlpha(0.2f);
```

The original `UiColor` is never modified:

```cpp
UiColor original(1.0f, 0.0f, 0.0f, 1.0f);
UiColor modified = original.WithAlpha(0.5f);
original.GetRgba().a;  // Still 1.0
```

## Retrieving Color Values

### GetRgba

Resolve the color to its RGBA components:

```cpp
UiColor color(0.1f, 0.2f, 0.3f, 0.4f);
Vector4 rgba = color.GetRgba();  // (0.1, 0.2, 0.3, 0.4)
```

For theme-based colors, the value is resolved from the current theme:

```cpp
UiColor primary = UiColor::PRIMARY;
Vector4 resolved = primary.GetRgba();  // Theme-defined RGBA

// Unknown IDs return transparent black
UiColor unknown("NonExistentColor");
unknown.GetRgba();  // Vector4::ZERO
```

### Implicit Conversion to Vector4

`UiColor` implicitly converts to `Vector4`:

```cpp
UiColor color(0.5f, 0.6f, 0.7f, 0.8f);
Vector4 vec = color;  // Implicit conversion

// Useful for APIs expecting Vector4
void SomeFunction(Vector4 color);
UiColor uiColor(1.0f, 0.0f, 0.0f, 1.0f);
SomeFunction(uiColor);  // Implicit conversion
```

### Copy and Move Semantics

`UiColor` supports standard copy and move operations:

```cpp
UiColor original(1.0f, 0.5f, 0.25f, 0.75f);
UiColor copy(original);       // Copy constructor
UiColor moved(std::move(original));  // Move constructor

UiColor assigned;
assigned = copy;  // Copy assignment
assigned = std::move(copy);  // Move assignment