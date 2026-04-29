## Overview

The Animation module provides a robust framework for choreographing dynamic UI changes in `View` components, enabling smooth transitions, property interpolations, and physics-based motion. It centers around the `Animation` class, which allows developers to define sequences of property changes that the DALi runtime executes asynchronously, ensuring high performance and visual fluidity.

## Declarative View Animations

The framework simplifies common UI transition tasks through the use of `ViewAnimationBridge` and `ViewAnimationSpec`. These classes allow for a fluent, chainable API that targets common visual properties, reducing the boilerplate required for standard UI movements and state changes.

```cpp
View myView = View::New();
ViewAnimationSpec spec;
spec.PositionX(100.0f, Duration(1.0f));
spec.Opacity(0.5f, Duration(1.0f));
Animation animation = Animation::New(1.0f);
spec.ApplyTo(animation, myView);
animation.Play();
```

The `ViewAnimationSpec` allows you to encapsulate a series of property animations into a reusable object. Methods like `PositionX`, `Opacity`, and `SizeWidth` can be chained to build a complex state transition. Once defined, the `ApplyTo` method is used to execute these stored animations on a specific `View` instance. For immediate, one-off interactions, the `ViewAnimationBridge` offers a similar functional interface that pairs an animation directly with a target view.

## Advanced Keyframe Choreography

For complex animations that cannot be represented by simple start-to-end transitions, developers should use the `KeyFrames` class. This allows you to define a property's value at specific progress points (0.0 to 1.0) along the duration of the animation.

```cpp
KeyFrames keyFrames = KeyFrames::New();
keyFrames.Add(0.0f, 0.0f);
keyFrames.Add(0.5f, 50.0f, AlphaFunction::BuiltinFunction::BOUNCE);
keyFrames.Add(1.0f, 100.0f);
Animation animation = Animation::New(2.0f);
animation.AnimateBetween(Actor::Property::POSITION_X, keyFrames);
animation.Play();
```

Once a set of keyframes is populated using the `Add` method, you can pass them to an `Animation` object using the `AnimateBetween` method. This provides granular control over the interpolation curve between stages. When adding keyframes, you can optionally provide an `AlphaFunction` for each segment to control the easing behavior individually between specific points.

## Path-Based Movement

Motion along non-linear trajectories is achieved using the `Path` class. This class represents a 3D parametric curve defined by interpolation points and control points, which a <!-- View --> can follow during an animation sequence.

```cpp
Path path = Path::New();
path.AddPoint(Vector3(0.0f, 0.0f, 0.0f));
path.AddPoint(Vector3(100.0f, 100.0f, 0.0f));
path.GenerateControlPoints(0.5f);
Animation animation = Animation::New(2.0f);
View myView = View::New();
animation.Animate(myView, path, Vector3(0.0f, 0.0f, 1.0f));
animation.Play();
```

To create a motion path, use `AddPoint` to define the primary waypoints. The system can automatically calculate smooth curves between these points using the `GenerateControlPoints` method. Once the path is defined, the `Animate` method on an `Animation` object accepts the `View` and the `Path` to synchronize the component's position and orientation with the trajectory.

## Dynamic Constraints and Physics

Constraints provide a powerful mechanism for reactive UI, where a property's value is automatically calculated based on one or more source properties. By using the `Constraint` class, you can establish permanent dependencies between different components.

```cpp
View targetView = View::New();
View sourceView = View::New();
Constraint constraint = Constraint::New<Vector3>(targetView, Actor::Property::POSITION, EqualToConstraint());
constraint.AddSource(ConstraintSource(Source(sourceView, Actor::Property::POSITION)));
constraint.Apply();
```

You can construct constraints by passing a callback function or a functor—such as `EqualToConstraint` or `RelativeToConstraint`—that processes input properties and updates the target. For physics-based feel, the `SpringData` class allows you to define stiffness, damping, and mass. This data can be incorporated into an `AlphaFunction` constructor to create animations that naturally overshoot and settle, mimicking physical objects.

## Easing and Temporal Control

The timing and pacing of animations are governed by the `AlphaFunction` and `TimePeriod` classes. An `AlphaFunction` determines how the animated value changes over time, supporting everything from simple linear movement to complex custom easing curves.

```cpp
Animation animation = Animation::New(1.0f);
AlphaFunction alpha(AlphaFunction::BuiltinFunction::EASE_IN_OUT);
TimePeriod period(0.5f, 1.0f);
View myView = View::New();
animation.AnimateTo(Actor::Property::OPACITY, 1.0f, alpha, period);
animation.Play();
```

You can construct an `AlphaFunction` using predefined constants from `AlphaFunction::BuiltinFunction` or by providing bezier control points. To manage the pacing of multiple sequences, the `TimePeriod` structure allows you to define explicit delays and durations. This is particularly useful when chaining operations or orchestrating groups of animations that must trigger at staggered intervals.

## Text Animation Patterns

The framework provides specialized support for animating text-based UI components through the `LabelAnimationBridge` and `LabelAnimationSpec` classes. These behave similarly to the general view equivalents but are optimized for properties specific to text, such as text color and borderline attributes.

```cpp
Label myLabel = Label::New();
LabelAnimationBridge bridge(Animation::New(1.0f), myLabel);
bridge.TextColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f), Duration(1.0f));
bridge.BorderlineColor(UiColor::PRIMARY, Duration(1.0f));
```

By using the `TextColor` or `TextColorBy` methods, you can seamlessly transition text colors as part of an animation sequence. These bridges maintain the same chainable syntax as other animation specs, ensuring that text elements integrate smoothly into your overall application choreography.