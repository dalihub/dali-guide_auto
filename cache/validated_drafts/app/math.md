## Overview

The DALi math module provides a comprehensive suite of geometric and linear algebra primitives designed for high-performance UI rendering, spatial positioning, and coordinate transformations. These utilities form the foundation for all visual calculations and layout computations within the framework.

## Vector and Coordinate Data

Spatial representation in the UI is handled through fixed-size vector types. These structures provide efficient storage and arithmetic operations for 2D, 3D, and 4D data points.

### Vector Classes
The module defines `Vector2`, `Vector3`, and `Vector4` to handle coordinate and color data. Each class supports fundamental operations such as vector addition, subtraction, multiplication, and division, alongside specialized geometric methods like `Dot`, `Cross`, and `Normalize`.

```cpp
Vector3 myVector(1.0f, 2.0f, 2.0f);
myVector.Normalize();
```

### Integer Pairs
For scenarios requiring discrete coordinate or dimension values, the module provides `Int32Pair` and `Uint16Pair`. These classes are used for passing around pairs of integers where float-based precision is not necessary.

```cpp
Int32Pair mySize(1920, 1080);
```

## Transformations and Matrix Operations

Transformations represent the spatial orientation and position of objects in 3D space. The module utilizes both 3x3 and 4x4 matrices to facilitate complex rendering operations.

### Matrix Utilities
The `Matrix` is used for representing full 4x4 transformations, including translation, rotation, and scale. It provides methods like `SetTransformComponents` for constructing a matrix from standard transformation data and `InvertTransform` for reversing coordinate changes. For lighter-weight 3x3 operations, the `Matrix3` is available.

```cpp
Matrix myMatrix;
Vector3 position(10.0f, 20.0f, 0.0f);
Vector3 scale(1.0f, 1.0f, 1.0f);
Quaternion rotation(Radian(0.0f), Vector3::YAXIS);
myMatrix.SetTransformComponents(scale, rotation, position);
```

## Rotations and Orientations

Smooth rotational behavior is managed through the `Quaternion`. Quaternions allow for flicker-free rotation and provide robust interpolation techniques between different states.

### Interpolation and Rotation
The module provides `Lerp`, `Slerp`, and `Squad` to calculate intermediate states between two orientations. You can also derive a quaternion from an angle-axis pair using the `AngleAxis` or extract rotational data back into Euler angles.

```cpp
Quaternion startRotation(Radian(0.0f), Vector3::YAXIS);
Quaternion endRotation(Radian(1.57f), Vector3::YAXIS);
Quaternion result = Quaternion::Slerp(startRotation, endRotation, 0.5f);
```

## Angular Conversions and Comparisons

To ensure consistent handling of angular data, the framework provides explicit types for Radians and Degrees. These types prevent units from being mixed unintentionally in mathematical functions.

### Angle Management
The `Radian` and `Degree` wrap float values, providing clear intent for input parameters. Conversion between these types is handled through explicit constructors.

```cpp
Degree myDegree(90.0f);
Radian myRadian(myDegree);
```

### Floating Point Precision
For robust comparisons of floating-point values, use the `Epsilon`. This compile-time utility allows you to define thresholds for equality checks in geometric calculations.

## Rectangular Region Management

The `Rect` handles layout-related tasks for rectangular areas, such as hit testing and bounding box calculations.

### Geometric Operations
Common tasks include checking if a point or another rectangle exists within a bounds via the `Contains` or calculating the shared region of two rectangles with the `Intersect`. You can also adjust existing regions using the `Inset` to shrink or expand the rectangle.

```cpp
Rect<int> rectA(0, 0, 100, 100);
Rect<int> rectB(25, 25, 50, 50);
bool isContained = rectA.Contains(rectB);
```

## Mathematical Utilities

Beyond geometric primitives, the module includes helper utilities for common procedural logic and randomization.

### Randomization
The `Random` provides functions to generate values in specific intervals. Use `Random::Range` to acquire a random float between two values, or `Random::Axis` to generate a normalized vector in a random 3D direction.

```cpp
float randomValue = Random::Range(0.0f, 1.0f);
```

### Logarithmic Calculations
Compile-time mathematical constants are provided through templates. These include the `Log` and the `Power`, which enable efficient, pre-calculated results for recurring mathematical operations required in visual effects.