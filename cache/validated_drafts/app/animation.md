# Overview

The DALi animation framework provides a powerful, high-performance system for creating fluid transitions and dynamic property changes across `View` components. By leveraging the animation system, developers can offload complex interpolation tasks to the engine, ensuring smooth visual feedback and consistent motion profiles throughout the application.

# Declarative UI Animations

Developers can define and reuse complex animation sequences for UI components using declarative specifications. This approach minimizes boilerplate code by allowing properties to be queued into a specification object before applying them to a target component.

### Using ViewAnimationSpec

The `ViewAnimationSpec` class provides a structured way to build a series of property modifications. Methods such as `PositionX`, `Opacity`, and `ScaleX` allow for precise control over the target value, duration, and easing. Once the desired chain of properties is defined, the `ApplyTo` method is used to bind the specification to an existing animation and a specific view.

```cpp
ViewAnimationSpec spec = ViewAnimationSpec::New();
spec.PositionX(100.0f, Duration(0.5f))
    .Opacity(0.0f, Duration(0.5f));
spec.ApplyTo(animation, view);
```

### Leveraging the ViewAnimationBridge

For scenarios requiring immediate, inline animation setup, the `ViewAnimationBridge` serves as a streamlined interface. It mirrors the fluent API of the specification classes but acts directly upon a view instance. This is particularly useful for ad-hoc UI feedback where creating a standalone specification object would be excessive.

```cpp
ViewAnimationBridge bridge(animation, view);
bridge.PositionX(200.0f, Duration(1.0f))
      .Opacity(0.5f, Duration(1.0f));
```

# Time-Based Property Interpolation

The core of the DALi animation system is the `Animation` class, which manages property transitions over a defined duration. Developers create an instance using `Animation::New`, which accepts the total duration in seconds.

### Keyframe Animations

Beyond simple start-to-end transitions, complex motion can be achieved using `KeyFrames`. By adding specific property values at various progress points—represented as floats between 0.0 and 1.0—the engine calculates smooth transitions between these defined states. The `AnimateBetween` method applies these keyframes to a target property, with options to specify interpolation types and alpha functions for each segment of the motion.

```cpp
KeyFrames keyFrames = KeyFrames::New();
keyFrames.Add(0.0f, Vector3(100.0f, 100.0f, 0.0f));
keyFrames.Add(1.0f, Vector3(200.0f, 200.0f, 0.0f));
animation.AnimateBetween(Actor::Property::SIZE, keyFrames);
```

# Motion Shaping and Easing

The feel of an animation is dictated by how its progress changes relative to time, a concept managed by `AlphaFunction`. DALi supports a variety of predefined motion curves and allows for custom configurations.

### Spring-Based Motion

To achieve organic, physics-inspired movement, the `SpringData` class allows developers to define motion based on physical properties. The `stiffness`, `damping`, and `mass` parameters determine how an object reaches its target, simulating spring-like behavior. This data can be passed to an `AlphaFunction` constructor to create custom spring easing effects.

```cpp
SpringData data(100.0f, 10.0f, 1.0f);
AlphaFunction alpha(data);
```

# Dynamic Relationship Constraints

Constraints enable reactive UI designs where property values are linked dynamically. Rather than manual updates, a `Constraint` defines a mathematical or logical relationship between a target property and one or more source properties.

### Implementing Logic with Constraints

Constraints are created using the `Constraint::New` static factory, which allows the use of functors or member methods to define how the input properties influence the output. The `Apply` or `ApplyPost` methods activate the constraint, ensuring the relationship is evaluated by the engine before or after the transform stage. 

> Note: The `Constraint::BAKE` action is used when a constraint is removed to finalize the property value at its current state.

```cpp
Constraint constraint = Constraint::New<float>(
    childView, 
    Actor::Property::OPACITY, 
    EqualToConstraint());
constraint.AddSource(Source(parentView, Actor::Property::POSITION_X));
constraint.Apply();
```

### Linear Constraints

For common proportional mapping, the `LinearConstrainer` simplifies the process of scaling a source value to a target property range. By using the `Apply` method, developers can establish a mapping from a source to a target property without writing custom constraint logic.

# Path-Based Movement

When movement requires non-linear trajectories, the `Path` class provides the foundation for 3D parametric curves. Developers add control points or interpolation points to define the shape of the path.

### Animating Along a Path

Once a path is defined, the `Animation::Animate` method allows an object to follow the trajectory. The engine handles the interpolation of the position and orientation along the curve based on the current animation progress. The `GenerateControlPoints` method can be called to ensure smooth transitions between segments of the path automatically.

```cpp
Path path = Path::New();
path.AddPoint(Vector3(0.0f, 0.0f, 0.0f));
path.AddPoint(Vector3(100.0f, 100.0f, 0.0f));
path.GenerateControlPoints(0.5f);
animation.Animate(view, path, Vector3::ZAXIS);
```

# Animation Control and Lifecycle

The lifecycle of an animation is controlled through a set of methods that manage playback state and behavior. The `Play`, `Pause`, and `Stop` methods provide direct control over the execution, while `SetLooping` and `SetLoopCount` manage repetitive behavior.

### Monitoring Completion

To trigger logic upon the conclusion of an animation, developers can connect to the `FinishedSignal`. This signal ensures that cleanup tasks or subsequent animations are initiated only when the current sequence has completely finished its execution.

```cpp
animation.FinishedSignal().Connect([](Animation& source) {
    // Animation completed
});
```