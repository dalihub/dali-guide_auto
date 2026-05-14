---
title: Visuals
sidebar_label: Visuals
category: images-visuals
---

# Visuals

Visuals are renderable objects that can be attached to a `View` to display images, colors, or animations. They provide a declarative way to compose visual elements within the view hierarchy.

## Table of Contents

- [Visual Types](#visual-types)
- [Attaching Visuals to Views](#attaching-visuals-to-views)
- [Transform and Positioning](#transform-and-positioning)
- [Corner Radius and Borderline](#corner-radius-and-borderline)
- [ImageVisual](#imagevisual)
- [AnimatedImageVisual](#animatedimagevisual)
- [ColorVisual](#colorvisual)

## Visual Types

The dali-ui framework provides several visual types:

- **ColorVisual** - Renders a solid color with optional blur and cutout effects
- **ImageVisual** - Displays static images with support for n-patch and masking
- **AnimatedImageVisual** - Plays animated image sequences (GIF, WebP, or image lists)

All visual types inherit from `VisualBase`, which provides common properties for positioning, sizing, and decoration.

## Attaching Visuals to Views

Visuals are attached to a `View` using the `Visuals()` method or `AddVisual()`. Each visual is placed in a specific container range that determines its rendering order relative to the view's content.

```cpp
View view = View::New()
  .SetRequestedWidth(200.0f)
  .SetRequestedHeight(200.0f)
  .Visuals(
    Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT,
    {
      ColorVisual::New()
        .SetColor(UiColor(0x3F0F0F).WithAlpha(0.2f))
        .SetWidth(1.0f)
        .SetHeight(1.0f)
        .SetProportionFlags(Visual::Transform::ProportionFlags::SIZE_PROPORTIONAL),
    });
```

The `ContainerRangeType` enum defines where the visual renders:

- `UNDER_BACKGROUND_EFFECT` - Below the view's background effect
- `BETWEEN_BACKGROUND_EFFECT_AND_BACKGROUND` - Between background effect and background
- `BETWEEN_BACKGROUND_AND_CONTENT` - Between background and content
- `BETWEEN_CONTENT_AND_DECORATION` - Between content and decoration
- `BETWEEN_DECORATION_AND_FOREGROUND_EFFECT` - Between decoration and foreground effect
- `OVER_FOREGROUND_EFFECT` - Above the view's foreground effect

You can also add visuals dynamically using `AddVisual()`:

```cpp
view.AddVisual(
  ImageVisual::New()
    .SetResourceUrl("image.png")
    .SetWidth(0.5f)
    .SetHeight(0.5f),
  Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT
);
```

To remove a visual, use `RemoveVisual()`:

```cpp
uint32_t count = view.GetVisualCount(Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT);
if (count > 0)
{
  auto visual = view.GetVisualAt(Visual::ContainerRangeType::BETWEEN_BACKGROUND_AND_CONTENT, count - 1);
  view.RemoveVisual(visual);
}
```

## Transform and Positioning

`VisualBase` provides properties to control the position and size of a visual within its parent view.

### Position

Use `SetOffsetX()` and `SetOffsetY()` to position the visual. By default, values are proportions of the parent view size.

```cpp
ColorVisual::New()
  .SetOffsetX(0.25f)  // 25% from the origin
  .SetOffsetY(0.25f)  // 25% from the origin
```

### Size

Use `SetWidth()` and `SetHeight()` to set the visual size. By default, these are proportional to the parent view.

```cpp
ImageVisual::New()
  .SetResourceUrl("image.png")
  .SetWidth(0.5f)   // 50% of parent width
  .SetHeight(0.5f)  // 50% of parent height
```

### Proportion Flags

The `SetProportionFlags()` method controls whether position and size values are interpreted as proportions or absolute values:

```cpp
ImageVisual::New()
  .SetResourceUrl("image.png")
  .SetOffsetX(10.0f)
  .SetOffsetY(20.0f)
  .SetWidth(100.0f)
  .SetHeight(100.0f)
  .SetProportionFlags(Visual::Transform::ProportionFlags::ALL)  // All values are proportional
```

Use `Visual::Transform::ProportionFlags::SIZE_PROPORTIONAL` to make only size values proportional while keeping offsets absolute.

### Origin and Pivot

The `SetOrigin()` and `SetPivot()` methods define the reference point for positioning:

```cpp
ColorVisual::New()
  .SetOrigin(Align::TOP_CENTER)   // Position relative to top-center of parent
  .SetPivot(Align::TOP_CENTER)    // Visual's top-center aligns to origin
```

### Extra Size

Use `SetExtraWidth()` and `SetExtraHeight()` to add absolute pixel values to the calculated size:

```cpp
ColorVisual::New()
  .SetWidth(1.0f)
  .SetHeight(1.0f)
  .SetProportionFlags(Visual::Transform::ProportionFlags::SIZE_PROPORTIONAL)
  .SetExtraWidth(10_spx)   // Add 10 pixels to width
  .SetExtraHeight(-5_spx)  // Subtract 5 pixels from height
```

## Corner Radius and Borderline

All visual types support corner radius and borderline decorations.

### Corner Radius

Set corner radius using `SetCornerRadius()`:

```cpp
ImageVisual::New()
  .SetResourceUrl("image.png")
  .SetCornerRadius(0.25f)  // 25% corner radius
  .SetCornerRadiusPolicyRelative()
```

For individual corner control:

```cpp
ColorVisual::New()
  .SetCornerRadius(0.25f, 0.0f, 0.0f, 0.25f)  // topLeft, topRight, bottomRight, bottomLeft
  .SetCornerRadiusPolicyRelative()
```

Use `SetCornerRadiusPolicy()` with `CornerRadiusPolicy::ABSOLUTE` for pixel values or `CornerRadiusPolicy::RELATIVE` for proportional values.

### Corner Squareness

Control the corner shape with `SetCornerSquareness()`:

```cpp
ColorVisual::New()
  .SetCornerRadius(0.25f)
  .SetCornerSquareness(0.6f)  // More squared corners
  .SetCornerRadiusPolicyRelative()
```

### Borderline

Add a border outline with `SetBorderlineWidth()`, `SetBorderlineColor()`, and `SetBorderlineOffset()`:

```cpp
ColorVisual::New()
  .SetColor(UiColor(0x003070))
  .SetCornerRadius(0.25f)
  .SetCornerRadiusPolicyRelative()
  .SetBorderlineWidth(2_spx)
  .SetBorderlineColor(UiColor(0x3F3F3F).WithAlpha(0.5f))
  .SetBorderlineOffset(-0.98f)
```

## ImageVisual

`ImageVisual` displays static images with support for various loading and rendering options.

### Basic Usage

```cpp
ImageVisual::New()
  .SetResourceUrl("image.png")
  .SetWidth(1.0f)
  .SetHeight(1.0f)
```

### Loading Options

Control image loading behavior with `SetSynchronousLoading()` and `SetLoadPolicy()`:

```cpp
ImageVisual::New()
  .SetResourceUrl("image.png")
  .SetSynchronousLoading(true)  // Load on main thread
  .SetLoadPolicy(Image::LoadPolicy::ATTACHED)  // Load when attached to view
```

### Sizing Options

Set desired dimensions or fitting mode:

```cpp
ImageVisual::New()
  .SetResourceUrl("image.png")
  .SetDesiredWidth(200)
  .SetDesiredHeight(150)
  .SetFittingMode(Image::FittingMode::FIT_KEEP_ASPECT_RATIO)
```

### Alpha Masking

Apply an alpha mask to the image:

```cpp
ImageVisual::New()
  .SetResourceUrl("image.png")
  .SetAlphaMaskUrl("mask.png")
  .SetMaskContentScale(1.0f)
  .SetCropToMask(true)
```

### N-Patch Support

For n-patch images, configure border properties:

```cpp
ImageVisual::New()
  .SetResourceUrl("npatch.9.png")
  .SetNPatchBorder(Dali::Extents(10, 10, 10, 10))
  .SetNPatchBorderOnly(false)
```

## AnimatedImageVisual

`AnimatedImageVisual` plays animated images from GIF, WebP, or a list of image URLs.

### Basic Usage

Load an animated GIF:

```cpp
AnimatedImageVisual::New()
  .SetResourceUrl("animation.gif")
  .SetWidth(1.0f)
  .SetHeight(1.0f)
  .Play()
```

### Image Sequence

Use a list of image URLs for frame-by-frame animation:

```cpp
AnimatedImageVisual::New()
  .SetResourceUrlList({
    "frame01.png",
    "frame02.png",
    "frame03.png",
  })
  .SetFrameDelay(100)  // 100ms between frames
  .SetWidth(1.0f)
  .Play()
```

### Playback Control

Control animation playback with `Play()`, `Pause()`, `Stop()`, and `JumpTo()`:

```cpp
AnimatedImageVisual visual = AnimatedImageVisual::New()
  .SetResourceUrl("animation.gif")
  .Play();

// Later, pause the animation
visual.Pause();

// Resume playback
visual.Play();

// Stop and reset
visual.Stop();

// Jump to a specific frame
visual.JumpTo(10);
```

### Loop Configuration

Set the number of times the animation repeats:

```cpp
AnimatedImageVisual::New()
  .SetResourceUrl("animation.gif")
  .SetLoopCount(3)  // Play 3 times
```

Use `-1` for infinite looping.

### Play Range

Limit playback to a specific frame range:

```cpp
Dali::Property::Array range;
range.PushBack(10);  // Start frame
range.PushBack(20);  // End frame

AnimatedImageVisual::New()
  .SetResourceUrl("animation.gif")
  .SetPlayRange(range)
  .Play()
```

### Speed Control

Adjust playback speed with `SetFrameSpeedFactor()`:

```cpp
AnimatedImageVisual::New()
  .SetResourceUrl("animation.gif")
  .SetFrameSpeedFactor(2.0f)  // Double speed
  .Play()
```

### Cache Configuration

Optimize performance for image sequences with batch and cache settings:

```cpp
AnimatedImageVisual::New()
  .SetResourceUrlList({"frame01.png", "frame02.png", "frame03.png"})
  .SetBatchSize(4)   // Load 4 frames at a time
  .SetCacheSize(6)   // Cache up to 6 frames
  .Play()
```

### Stop Behavior

Control how the animation stops:

```cpp
AnimatedImageVisual::New()
  .SetResourceUrl("animation.gif")
  .SetStopBehavior(AnimatedImage::StopBehavior::CURRENT_FRAME)  // Stop at current frame
  .Play()
```

### Querying State

Get the current playback state and frame information:

```cpp
AnimatedImage::PlayState state = visual.GetPlayState();
int currentFrame = visual.GetCurrentFrameNumber();
int totalFrames = visual.GetTotalFrameNumber();
```

## ColorVisual

`ColorVisual` renders a solid color with optional blur and cutout effects.

### Basic Usage

```cpp
ColorVisual::New()
  .SetColor(UiColor(0xFF0000))  // Red
  .SetWidth(1.0f)
  .SetHeight(1.0f)
```

### Blur Effect

Apply a blur effect to the color:

```cpp
ColorVisual::New()
  .SetColor(UiColor(0x3F0F0F).WithAlpha(0.2f))
  .SetBlurRadius(12_spx)
  .SetWidth(1.0f)
  .SetHeight(1.0f)
```

### Cutout Policy

Control how the visual interacts with the view's shape:

```cpp
ColorVisual::New()
  .SetColor(UiColor(0x000000).WithAlpha(0.5f))
  .SetCutoutPolicy(CutoutPolicy::CUTOUT_VIEW_WITH_CORNER_RADIUS)
```

Available cutout policies:

- `CUTOUT_VIEW_WITH_CORNER_RADIUS` - Cut out the view area with corner radius
- `CUTOUT_OUTSIDE_WITH_CORNER_RADIUS` - Cut out outside the view area

### Shadow Effects

Combine blur and cutout policies to create shadow effects:

```cpp
View view = View::New()
  .SetCornerRadius(0.25f)
  .SetCornerRadiusPolicyRelative()
  .Visuals(
    Visual::ContainerRangeType::BETWEEN_BACKGROUND_EFFECT_AND_BACKGROUND,
    {
      ColorVisual::New()
        .SetName("Shadow")
        .SetOffsetX(10_spx)
        .SetOffsetY(10_spx)
        .SetWidth(1.0f)
        .SetHeight(1.0f)
        .SetProportionFlags(Visual::Transform::ProportionFlags::SIZE_PROPORTIONAL)
        .SetBlurRadius(12_spx)
        .SetCornerRadius(12_spx)
        .SetColor(UiColor(0x000000).WithAlpha(0.3f))
        .SetCutoutPolicy(CutoutPolicy::CUTOUT_VIEW_WITH_CORNER_RADIUS),
    });