---
title: Ui Color
sidebar_label: Ui Color
category: styling-theme-config
---

# Ui Color

UiColor provides a theme-aware color abstraction for dali-ui applications. It supports both explicit RGBA values and theme color references that resolve at runtime.

## Table of Contents

- [Creating UiColor Instances](#creating-uicolor-instances)
- [Theme Color References](#theme-color-references)
- [Alpha Manipulation](#alpha-manipulation)
- [Predefined Theme Colors](#predefined-theme-colors)
- [Converting to Vector4](#converting-to-vector4)

## Creating UiColor Instances

UiColor offers multiple constructors for different use cases.

### RGBA Values

Create a UiColor with explicit red, green, blue, and alpha components. Values are in the range 0.0 to 1.0.

```cpp
// Full RGBA
UiColor red(1.0f, 0.0f, 0.0f, 1.0f);

// RGB with default alpha of 1.0
UiColor gray(0.5f, 0.5f, 0.5f);
```

### Hex RGB Values

Create a UiColor from a hexadecimal RGB value. The alpha defaults to 1.0.

```cpp
// 0xFF0000 = pure red
UiColor red(0xFF0000u);

// Hex with explicit alpha
UiColor translucentGreen(0x00FF00u, 0.5f);
```

### Vector4 Values

Construct a UiColor from an existing Vector4.

```cpp
Vector4 input(0.2f, 0.4f, 0.6f, 0.8f);
UiColor color(input);
```

### Default Construction

A default-constructed UiColor has no color ID and resolves to transparent black (Vector4::ZERO).

```cpp
UiColor color;
// color.HasColorId() == false
// color.GetRgba() == Vector4::ZERO
```

## Theme Color References

UiColor can reference theme colors by string identifier. The actual RGBA value is resolved at runtime through the theme system.

### Creating Theme Color References

```cpp
// Reference a theme color by ID
UiColor themeColor("ThemeColor1");

// Check if the color is a theme reference
if (themeColor.HasColorId()) {
    String id = themeColor.GetColorId(); // "ThemeColor1"
}
```

### Resolving Theme Colors

Call `GetRgba()` to resolve the color to its RGBA value. Unknown theme IDs return Vector4::ZERO.

```cpp
UiColor color("Primary");
Vector4 resolved = color.GetRgba();

// Unknown theme colors fall back to transparent
UiColor unknown("NonExistentColorId");
Vector4 fallback = unknown.GetRgba(); // Vector4::ZERO
```

### Using with Views

Apply UiColor to view backgrounds for theme-aware styling.

```cpp
// Use predefined theme colors
View view = View::New()
    .SetBackgroundColor(UiColor::PRIMARY);

// Use custom theme color
View themedView = View::New()
    .SetBackgroundColor(UiColor("ThemeColor2"));

// Apply to layout
Layout layout = Layout::New()
    .SetBackgroundColor(UiColor::BACKGROUND);
```

## Alpha Manipulation

UiColor provides fluent methods for adjusting transparency while preserving theme references.

### WithAlpha

`WithAlpha()` sets an absolute alpha value, clamped to the range [0.0, 1.0]. The original UiColor is unchanged.

```cpp
UiColor solid(1.0f, 0.0f, 0.0f, 1.0f);
UiColor translucent = solid.WithAlpha(0.3f);
// solid.GetRgba().a == 1.0f (unchanged)
// translucent.GetRgba().a == 0.3f
```

When applied to a theme color reference, the color ID is preserved:

```cpp
UiColor primary = UiColor::PRIMARY.WithAlpha(0.7f);
// primary.HasColorId() == true
// primary.GetColorId() == "Primary"
```

### ScaleAlpha

`ScaleAlpha()` multiplies the current alpha by a factor, clamped to [0.0, 1.0]. The original UiColor is unchanged.

```cpp
UiColor color(1.0f, 0.0f, 0.0f, 0.8f);
UiColor result = color.ScaleAlpha(0.5f);
// result.GetRgba().a == 0.4f (0.8 * 0.5)
```

Theme color references are preserved:

```cpp
UiColor scaled = UiColor::PRIMARY.ScaleAlpha(0.5f);
// scaled.HasColorId() == true
// scaled.GetColorId() == "Primary"
```

### Fluent Chaining

Chain alpha methods for complex transformations:

```cpp
// WithAlpha then ScaleAlpha: 0.5 * 0.2 = 0.1
UiColor result = UiColor::PRIMARY.WithAlpha(0.5f).ScaleAlpha(0.2f);

// Multiple ScaleAlpha calls multiply together
UiColor chained = UiColor(1.0f, 0.0f, 0.0f, 1.0f)
    .ScaleAlpha(0.5f)
    .ScaleAlpha(0.2f);
// chained.GetRgba().a == 0.1f
```

### Practical Example

Create semi-transparent colors for overlays and effects:

```cpp
const UiColor BACKGROUND_TEXT_COLOR = UiColor(0xC8D7E8).WithAlpha(0.38f);
const UiColor STATUS_TEXT_COLOR = UiColor(0xF6F0DE).WithAlpha(0.80f);
const UiColor PLAY_BORDER_COLOR = UiColor(0x6EDCFF).WithAlpha(0.26f);
```

## Predefined Theme Colors

UiColor provides static constants for common theme colors.

### PRIMARY

The primary brand color.

```cpp
UiColor primary = UiColor::PRIMARY;
// primary.HasColorId() == true
// primary.GetColorId() == "Primary"
```

### BACKGROUND

The default background color for containers.

```cpp
Layout layout = Layout::New()
    .SetBackgroundColor(UiColor::BACKGROUND);
```

### OUTLINE

The outline color for borders and text effects.

```cpp
Text::Outline outline = Text::Outline()
    .SetColor(UiColor::OUTLINE.WithAlpha(0.48f))
    .SetWidth(1.5f)
    .SetBlurRadius(1.6f);
```

## Converting to Vector4

UiColor implicitly converts to Vector4 for compatibility with APIs that expect raw color values.

```cpp
UiColor color(0.5f, 0.6f, 0.7f, 0.8f);
Vector4 vec = color; // implicit conversion

// Use with APIs expecting Vector4
label.SetTextColor(color); // implicit conversion to Vector4
```

Explicit conversion is also available via `GetRgba()`:

```cpp
UiColor themeColor("Primary");
Vector4 resolved = themeColor.GetRgba();
```
