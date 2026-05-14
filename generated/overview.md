# DALi Overview

## What DALi Is

DALi (Dynamic Animation Library) is a cross-platform UI framework written in C++ for building hardware-accelerated user interfaces. It provides a retained-mode scene graph architecture where UI elements persist in a tree structure and the framework handles rendering, layout, and input processing.

The framework consists of three layered repositories:

- **dali-core** provides the rendering foundation: a scene graph of actors, property animation, image loading, and GPU resource management
- **dali-adaptor** provides the platform integration layer: application lifecycle, window management, input event dispatching, and device status monitoring
- **dali-ui** provides the UI component layer: a View-based widget system with automatic layout, theming, and built-in interaction patterns

Applications typically work with the dali-ui layer, which builds on the lower layers to offer higher-level abstractions.

## How DALi Is Organized

The dali-ui library is divided into two sub-libraries:

- **dali-ui-foundation** provides core abstractions: the base [View](view.md) class, [layout containers](layouts.md), [visuals](visuals.md), [text rendering](text.md), [theming](ui-color-manager.md), and utility types
- **dali-ui-components** provides ready-to-use controls: buttons, sliders, and composite components built on the foundation layer

The foundation layer introduces the **Trait system**, which allows attaching composable behaviors to Views. For example, [InteractiveView](interactive-view.md) adds click handling, and the selectable trait adds selection state management.

## The App-Facing Model

Applications interact with DALi through a View hierarchy. A [View](view.md) is the fundamental UI object that participates in layout, renders visuals, and responds to input. Views are arranged in a parent-child tree where each View's size and position are determined by its parent container's layout algorithm.

The layout system uses a Measure/Arrange pass model. Views request sizes using `WRAP_CONTENT` (size to content) or `MATCH_PARENT` (fill parent), and parent layouts compute final positions. Four layout containers are provided:

- [AbsoluteLayout](layouts.md) positions children at explicit coordinates
- [StackLayout](layouts.md) arranges children in a single row or column
- [FlexLayout](layouts.md) provides flexbox-style layout with grow, shrink, and wrap
- [GridLayout](layouts.md) arranges children in a grid with rows and columns

Views display content through **visuals**—renderable objects attached to the view. [ImageVisual](visuals.md) displays images, [ColorVisual](visuals.md) fills with solid color, and [AnimatedImageVisual](visuals.md) plays animated sequences. Multiple visuals can be layered on a single View.

## Interaction, Motion, and Presentation

DALi provides built-in patterns for common UI behaviors:

**Input and Focus** — [InteractiveView](interactive-view.md) handles clicks and long presses. [FocusManager](focus-manager.md) provides keyboard navigation with directional focus movement and focus groups. [ViewState](view-state.md) tracks interactive states like focused, pressed, and disabled.

**Animation** — The [Animation](animation.md) API animates any animatable property on a View using alpha functions (easing curves), key frames, and looping. Animations can target position, size, opacity, color, and custom properties.

**Theming** — [UiColorManager](ui-color-manager.md) provides theme-driven color lookups. [UiColor](ui-color.md) represents colors that can be explicit RGBA values or theme references resolved at runtime.

**Images** — [ImageView](image-view.md) displays static images with fitting modes and masking. [AnimatedImageView](animated-image-view.md) plays GIF and animated WebP files. [LottieAnimationView](lottie-animation-view.md) renders vector animations from After Effects exports.

**Text** — [Label](label.md) displays non-editable text with multi-line layout, marquee scrolling, and styling effects. [InputField](input-field.md) provides single-line text input with cursor and selection management.

**Scrolling** — [ScrollView](scroll-view.md) provides a scrollable viewport for content larger than the visible area, with fling behavior and scroll bars.

## Where To Go Next

The guides in this documentation are organized by functional area. Start with [View (Base UI Object)](view.md) to understand the core View API, then explore [Layouts](layouts.md) to learn how to arrange Views. For interactive components, see [InteractiveView](interactive-view.md) and [FocusManager](focus-manager.md). For visual content, see [ImageView](image-view.md), [Label](label.md), and [Animation](animation.md).

![DALi Architecture Overview](assets/dali-overview-at-a-glance.svg)

