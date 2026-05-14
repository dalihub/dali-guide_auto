-----BEGIN_REVISED_MARKDOWN-----
---
title: Ui Color
sidebar_label: Ui Color
category: styling-theme-config
---

# Ui Color

`UiColor` provides a theme-aware color abstraction for dali-ui applications. It supports both explicit RGBA values and theme color identifiers that resolve at runtime, enabling consistent styling and dynamic theme switching.

## Table of Contents

- [Creating UiColor Objects](#creating-uicolor-objects)
- [Predefined Theme Colors](#predefined-theme-colors)
- [Resolving Color Values](#resolving-color-values)
- [Modifying Alpha](#modifying-alpha)
- [Using UiColor with Views](#using-uicolor-with-views)

## Creating UiColor Objects

### Default Constructor

Creates a `UiColor` with transparent black (all components 0.0) and no color ID.

```cpp
UiColor transparent;
// transparent.GetRgba() == Vector4::ZERO
// transparent.HasColorId() == false
```

### RGBA Constructor

Create a `UiColor` from red, green, blue, and alpha components. Each component is a float in the range `[0.0, 1.0]`. The alpha parameter defaults to `1.0f` (fully opaque).

```cpp
// Fully opaque red
UiColor red(1.0f, 0.0f, 0.0f);

// Semi-transparent blue with explicit alpha
UiColor blue(0.0f, 0.0f, 1.0f, 0.7f);
```

### Vector4 Constructor

Create a `UiColor` from a `Vector4` containing RGBA components.

```cpp
Vector4 rgba(0.2f, 0.4f, 0.6f, 0.8f);
UiColor color(rgba);
```

### Hex RGB Constructor

Create a `UiColor` from a 24-bit RGB hex value. The alpha defaults to `1.0f`.

```cpp
// Pure red (0xFF0000)
UiColor red(0xFF0000u);

// Custom color with separate alpha
UiColor custom(0x387AFFu, 0.5f);
```

### Theme Color Identifier

Create a `UiColor` that references a named theme color. The actual RGBA value is resolved at runtime from the current theme.

```cpp
// Reference a theme color by name
UiColor themeColor("ThemeColor1");

// Move semantics for efficiency
UiColor movedColor(std::move(String("AnotherColor")));
```

Use `HasColorId()` to check if a `UiColor` holds a theme identifier rather than explicit RGBA values:

```cpp
UiColor explicit(1.0f, 0.0f, 0.0f);
UiColor themed("Primary");

bool isExplicit = explicit.HasColorId(); // false
bool isThemed = themed.HasColorId();     // true
```

## Predefined Theme Colors

`UiColor` provides static constants for common theme colors. These are theme identifiers, not fixed RGBA values.

```cpp
// Primary accent color
UiColor primary = UiColor::PRIMARY;

// Background color for containers
UiColor background = UiColor::BACKGROUND;

// Outline or border color
UiColor outline = UiColor::OUTLINE;
```

Each predefined constant holds a color ID:

```cpp
String primaryId = UiColor::PRIMARY.GetColorId();     // "Primary"
String backgroundId = UiColor::BACKGROUND.GetColorId(); // "Background"
String outlineId = UiColor::OUTLINE.GetColorId();     // "Outline"
```

## Resolving Color Values

### GetRgba

Call `GetRgba()` to resolve a `UiColor` to its RGBA components. For theme colors, this returns the current theme's resolved value. Unknown theme identifiers return `Vector4::ZERO`.

```cpp
UiColor color(0.2f, 0.4f, 0.6f, 0.8f);
Vector4 rgba = color.GetRgba();
// rgba.r == 0.2f, rgba.g == 0.4f, rgba.b == 0.6f, rgba.a == 0.8f
```

For theme colors, resolution depends on the active theme:

```cpp
UiColor primary = UiColor::PRIMARY;
Vector4 resolved = primary.GetRgba(); // Resolves to theme's Primary color
```

### Implicit Conversion to Vector4

`UiColor` supports implicit conversion to `Vector4`, allowing direct use where `Vector4` color parameters are expected:

```cpp
UiColor color(0.5f, 0.6f, 0.7f, 0.8f);
Vector4 vec = color; // Implicit conversion
```

## Modifying Alpha

### WithAlpha

`WithAlpha(float alpha)` returns a new `UiColor` with the alpha set to the specified value. The original color is unchanged. Alpha values are clamped to `[0.0, 1.0]`.

```cpp
UiColor original(1.0f, 0.0f, 0.0f, 1.0f);
UiColor semi = original.WithAlpha(0.5f);

// original.GetRgba().a == 1.0f (unchanged)
// semi.GetRgba().a == 0.5f
```

For theme colors, `WithAlpha` preserves the color ID:

```cpp
UiColor themed = UiColor::PRIMARY.WithAlpha(0.7f);
bool stillThemed = themed.HasColorId(); // true
```

### ScaleAlpha

`ScaleAlpha(float factor)` returns a new `UiColor` with the alpha multiplied by the given factor. The result is clamped to `[0.0, 1.0]`.

```cpp
UiColor original(1.0f, 0.0f, 0.0f, 0.8f);
UiColor scaled = original.ScaleAlpha(0.5f);
// scaled.GetRgba().a == 0.4f (0.8 * 0.5)
```

### Chaining Alpha Operations

`WithAlpha` and `ScaleAlpha` can be chained for fluent alpha manipulation:

```cpp
// Set alpha to 0.5, then scale by 0.2 -> final alpha = 0.1
UiColor result = UiColor::PRIMARY.WithAlpha(0.5f).ScaleAlpha(0.2f);

// Scale by 0.5, then override to 0.3
UiColor overrideResult = UiColor::PRIMARY.ScaleAlpha(0.5f).WithAlpha(0.3f);
```

## Using UiColor with Views

`UiColor` integrates with the View-based object model for setting background colors:

```cpp
// Set background using a predefined theme color
View view = View::New()
  .SetBackgroundColor(UiColor::PRIMARY);

// Set background using a custom RGBA color
View customView = View::New()
  .SetBackgroundColor(UiColor(0xFF5C8Au));

// Set background using a theme color identifier
View themedView = View::New()
  .SetBackgroundColor(UiColor("ThemeColor1"));
```

Use `UiColor` with layout containers:

```cpp
Layout container = Layout::New()
  .SetBackgroundColor(UiColor::BACKGROUND)
  .SetRequestedWidth(200_spx)
  .SetRequestedHeight(200_spx);
```

Combine alpha modification with view styling:

```cpp
// Semi-transparent background
View overlay = View::New()
  .SetBackgroundColor(UiColor(0x000000u).WithAlpha(0.5f));

// Scaled alpha for subtle backgrounds
View subtle = View::New()
  .SetBackgroundColor(UiColor::BACKGROUND.ScaleAlpha(0.3f));
```

Update colors dynamically in response to user interaction:

```cpp
View clickable = View::New()
  .SetBackgroundColor(UiColor::PRIMARY)
  .AsInteractive([this](InteractiveTrait& trait) {
    trait.ClickedSignal().Connect(this, [this](View view, InputEvent event) -> bool {
      view.SetBackgroundColor(UiColor("ThemeColor1"));
      return true;
    });
  });
```
-----END_REVISED_MARKDOWN-----

-----BEGIN_PROSE_REVIEW_REPORT-----
# Ui Color Prose Review

## Summary

The draft was largely accurate against the public API headers and UTC evidence. Two omissions were identified and corrected. All existing code blocks were verified against source and preserved.

## Changes Made

### 1. Added Default Constructor Section
- **Location**: Creating UiColor Objects section
- **Change**: Added new subsection documenting `UiColor()` default constructor
- **Source Evidence**: Header shows `UiColor()` creates default UiColor with comment "Creates a default UiColor (transparent black, no ID)."
- **UTC Evidence**: `UtcDaliUiColorDefaultConstructorP` confirms `GetRgba()` returns `Vector4::ZERO` and `HasColorId()` returns false

### 2. Added Vector4 Constructor Section
- **Location**: Creating UiColor Objects section
- **Change**: Added new subsection documenting `UiColor(const Vector4& color)` constructor
- **Source Evidence**: Header shows `UiColor(const Vector4& color)` constructor with comment "Creates a UiColor from a Vector4."
- **UTC Evidence**: `UtcDaliUiColorVector4ConstructorP` confirms the constructor works correctly

## Verified Accurate (No Changes Needed)

### RGBA Constructor
- Header signature: `UiColor(float r, float g, float b, float a = 1.0f)` ✓
- Default alpha value 1.0f confirmed in header and UTC

### Hex RGB Constructor
- Header signature: `explicit UiColor(uint32_t rgb, float a = 1.0f)` ✓
- UTC `UtcDaliUiColorHexConstructorP` confirms 0xFF0000u produces red (1.0, 0.0, 0.0)

### Theme Color Identifier Constructors
- Header signatures: `explicit UiColor(const String& colorId)` and `explicit UiColor(String&& colorId)` ✓
- UTC confirms both lvalue and rvalue overloads work correctly

### Predefined Theme Colors
- Header declares: `static const UiColor PRIMARY`, `BACKGROUND`, `OUTLINE` ✓
- UTC `UtcDaliUiColorPredefinedPrimaryP` confirms `PRIMARY.GetColorId()` returns "Primary"
- UTC confirms BACKGROUND returns "Background", OUTLINE returns "Outline"

### HasColorId() and GetColorId()
- Header signatures match draft ✓
- UTC tests confirm behavior for both RGBA and token types

### GetRgba() and Implicit Conversion
- Header signatures match draft ✓
- UTC confirms unknown tokens return `Vector4::ZERO`

### WithAlpha() and ScaleAlpha()
- Header signatures match draft ✓
- UTC confirms both methods return new UiColor without modifying original
- UTC confirms clamping to [0.0, 1.0]
- UTC confirms token-based colors preserve color ID after alpha operations

### Chaining Behavior
- UTC `UtcDaliUiColorWithAlphaThenScaleAlphaTokenP` and related tests confirm chaining works as documented

### View Integration Examples
- Sample file `color-controls-example.cpp` confirms usage patterns:
  - `View::New().SetBackgroundColor(UiColor::PRIMARY)` ✓
  - `Layout::New().SetBackgroundColor(UiColor::BACKGROUND)` ✓
  - `.AsInteractive()` with `ClickedSignal().Connect()` pattern ✓

## Remaining Concerns

None. All documented APIs are verified against public headers and UTCs. Code examples match sample file patterns.

## Non-English Prose Check

No non-English prose found in the draft.
-----END_PROSE_REVIEW_REPORT-----