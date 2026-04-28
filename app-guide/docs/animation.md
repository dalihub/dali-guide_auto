---
id: animation
title: "animation"
sidebar_label: "animation"
---
# Overview

The [animation](./animation.md) module provides a high-level framework for defining temporal changes in UI state, allowing developers to create fluid, performant transitions for Dali::Ui::View components. By leveraging this system, you can manipulate view properties like position, scale, opacity, and color over defined periods to produce complex motion graphics.

# Declarative View Animations

The framework enables a declarative approach to [animation](./animation.md) via specification objects, which are designed to decouple the definition of an [animation](./animation.md) from the execution on a target UI element. The `ViewAnimationSpec` allows you to configure a set of property transitions that can be applied to a view later. You can create a new specification by calling the `New()` on the class. Once defined, these transitions are enacted on a specific view using the `ApplyTo(Animation, View)`, which requires an active [animation](./animation.md) instance and the target view.

```cpp
ViewAnimationSpec animationSpec = ViewAnimationSpec::New();
animationSpec.PositionX(100.0f, Duration(1.0f));
animationSpec.Opacity(0.5f, Duration(1.0f));

Animation animation = Animation::New(1.0f);
animationSpec.ApplyTo(animation, myView);
animation.Play();
```

# Animating Sequences and Keyframes

For non-linear transitions or complex motion paths, you can orchestrate multi-point updates over time. The `KeyFrames` acts as a container for individual property states, where each state is associated with a specific progress point between zero and one. You can populate this container using the `Add(float, Property::Value)`. Once the keyframes are configured, you animate the property by passing the keyframe object to the `AnimateBetween(Property, KeyFrames)` of an animation instance.

```cpp
KeyFrames scaleKeyFrames = KeyFrames::New();
scaleKeyFrames.Add(0.0f, Vector3(1.0f, 1.0f, 1.0f));
scaleKeyFrames.Add(0.5f, Vector3(2.0f, 2.0f, 2.0f));
scaleKeyFrames.Add(1.0f, Vector3(1.0f, 1.0f, 1.0f));

Animation animation = Animation::New(2.0f);
animation.AnimateBetween(Actor::Property::SCALE, scaleKeyFrames);
animation.Play();
```

# Temporal Control and Timing

Effective animation requires precise control over how values evolve and how long they persist. You define the duration of an animation using the `Duration`, which can be initialized in seconds. The feel of the motion is dictated by the `AlphaFunction`, which maps the linear progress of time to a non-linear curve. You can select from built-in patterns or define custom bezier curves using the `AlphaFunction(BuiltinFunction)` that accepts two control points. Furthermore, the `TimePeriod` allows you to specify a delay and a duration for a sub-segment of an animation, providing granular control over when specific changes begin and end.

```cpp
Duration duration = Duration(2.5f);
AlphaFunction alpha = AlphaFunction(AlphaFunction::EASE_IN_OUT);
TimePeriod period = TimePeriod(0.5f, 2.5f);

Animation animation = Animation::New(3.0f);
animation.AnimateTo(Actor::Property::OPACITY, 0.0f, alpha, period);
animation.Play();
```

# Constraint-Based Motion

Constraints allow for reactive UI behavior where a property value is derived automatically from the state of other properties. The `Constraint` establishes these relationships by linking a target property to one or more sources. You can define the source of the data using the `ConstraintSource`, which can represent local properties, parent properties, or arbitrary object properties. Relationships are applied by providing a functor or a function pointer to the `New(Handle, Property::Index, const T &)` of the constraint, which performs the calculation on each frame.

```cpp
Constraint constraint = Constraint::New(myView, Actor::Property::POSITION_X, RelativeToConstraint(0.5f));
constraint.AddSource(ConstraintSource(ParentSource(Actor::Property::SIZE)));
constraint.Apply();
```

# Advanced Motion Dynamics

To achieve realistic, physics-based movement, the framework supports spring dynamics. You can define custom spring behavior using the `SpringData`, which exposes the stiffness, damping, and mass properties of the motion. These parameters can be passed into an `AlphaFunction(const SpringData &)` to create a spring-based easing curve. For mapping one range of values linearly to another, the `LinearConstrainer` provides a high-level interface to establish a proportional link between two properties.

```cpp
SpringData springData(100.0f, 10.0f, 1.0f);
AlphaFunction springAlpha(springData);

Animation animation = Animation::New(1.0f);
animation.AnimateTo(Actor::Property::POSITION_X, 200.0f, springAlpha);
animation.Play();
```

# UI-Specific Animation Bridges

For common UI elements, the framework provides specialized bridges that simplify the syntax for applying animations to complex components. The `ViewAnimationBridge` and the `LabelAnimationBridge` provide chainable methods for animating specific properties like `BackgroundColor(const UiColor &, Duration, AlphaFunction, Duration)`, `CornerRadius(const Vector4 &, Duration, AlphaFunction, Duration)`, or `TextColor(const UiColor &, Duration, AlphaFunction, Duration)`. These bridges significantly reduce the boilerplate required for property-by-property animation definition by wrapping the underlying animation logic in a fluent interface.

```cpp
LabelAnimationBridge bridge = LabelAnimationBridge(animation, myLabel);
bridge.BackgroundColor(UiColor::PRIMARY, Duration(0.5f))
      .Opacity(0.8f, Duration(0.5f))
      .BorderlineWidth(2.0f, Duration(0.5f));
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/animation)
