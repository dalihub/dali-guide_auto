---
id: math
title: "math"
sidebar_label: "math"
---
## Overview

The DALi [math](./math.md) module provides a robust suite of geometric and linear algebra primitives designed to handle the coordinate, transformation, and spatial requirements of high-performance UI applications. These tools serve as the foundation for positioning, animating, and [rendering](./rendering.md) graphical components within the 3D [scene](./scene.md) graph.

## Working with 2D and 3D Vectors

The framework utilizes specialized vector structures to manage positional, directional, and color data in application [layouts](./layouts.md). The classes `Vector2`, `Vector3`, and `Vector4` support standard arithmetic operations and geometric utilities like length calculations and normalization.

Vectors can be initialized with individual scalar components or via conversion constructors that support resizing between dimensions. Common operations such as dot products and cross products are available for calculating intersections, alignment, and directional surface properties. For clamping values within a defined boundary, the `Clamp(const Vector2 &, const Vector2 &)` allows restricting vector components to specific minimum and maximum ranges.

```cpp
Vector3 myVector(1.0f, 2.0f, 2.0f);
myVector.Normalize();
```

## Geometric Transformations and Matrices

To manipulate UI elements within a 3D coordinate system, the module provides the `Matrix` and `Matrix3`. These classes facilitate complex operations such as scaling, rotation, translation, and coordinate system inversions.

A transformation matrix can be configured using specialized component methods. The `SetTransformComponents(const Vector3 &, const Quaternion &, const Vector3 &)` updates the matrix to represent a specific combination of scale, rotation, and translation. Conversely, developers can extract these values from an existing transform using `GetTransformComponents(Vector3 &, Quaternion &, Vector3 &)`. For cases requiring inverse transformations, the `InvertTransform(Matrix &)` or `SetInverseTransformComponents(const Vector3 &, const Quaternion &, const Vector3 &)` ensures that spatial operations remain accurate during complex hierarchy updates.

```cpp
Matrix myMatrix;
myMatrix.SetTransformComponents(Vector3(1.0f, 1.0f, 1.0f), Quaternion(), Vector3(0.0f, 0.0f, 0.0f));
Matrix inverseMatrix;
myMatrix.InvertTransform(inverseMatrix);
```

## Rotations and Orientations

Smooth, gimbal-lock-free rotations are achieved using the `Quaternion`. This class supports representation through Euler angles, axis-angle pairs, or direct matrix conversion, making it the preferred method for animating 3D object orientations.

Interpolation between orientations is handled through several key methods. Linear interpolation is provided by `Lerp(const Quaternion &, const Quaternion &, float)`, while spherical linear interpolation is performed by `Slerp(const Quaternion &, const Quaternion &, float)` to maintain constant rotational velocity. For complex animation paths, the `Squad(const Quaternion &, const Quaternion &, const Quaternion &, const Quaternion &, float)` enables spherical cubic interpolation across control points. If the shortest rotational distance between two orientations is required, the `AngleBetween(const Quaternion &, const Quaternion &)` calculates this value in radians.

```cpp
Quaternion q1 = Quaternion(Radian(0.0f), Vector3::XAXIS);
Quaternion q2 = Quaternion(Radian(1.57f), Vector3::XAXIS);
Quaternion result = Slerp(q1, q2, 0.5f);
```

## Defining Spatial Areas and Bounds

Spatial management for screen regions and hit-testing is performed using the `Rect`. This template-based structure stores coordinate and dimension data, providing utility methods to assess the state of rectangular boundaries.

Developers can determine the validity of a region using the `IsValid()` or check if it is empty via the `IsEmpty()`. Geometric interactions such as determining if two areas overlap are supported by the `Intersects(const Rect< T > &)` and `Intersect(const Rect< T > &)`. Furthermore, modifying the size of a rectangle without affecting its center position is made simple with the `Inset(T, T)`.

```cpp
Rect<float> rect1(0.0f, 0.0f, 100.0f, 100.0f);
Rect<float> rect2(50.0f, 50.0f, 100.0f, 100.0f);
if(rect1.Intersects(rect2))
{
  rect1.Inset(10.0f, 10.0f);
}
```

## Angle and Precision Utilities

To maintain consistent mathematical operations across the framework, dedicated wrappers are provided for angle units, specifically `Degree` and `Radian`. These allow for type-safe conversion between units, preventing errors in rotation calculations.

Floating-point precision issues are addressed using the `Epsilon`. This compile-time template utility allows developers to identify the machine epsilon, which is essential for implementing safe comparisons in logical checks involving floating-point numbers.

```cpp
Degree myDegree(90.0f);
Radian myRadian(myDegree);
bool isEqual = (myRadian < (Radian(1.57f) + Epsilon<1>::value));
```

## Data Packing and Pair Structures

Efficient memory handling and grid-based layout calculations are supported by specialized pair structures. The `IntPair`, `Int32Pair`, and `Uint16Pair` provide compact storage for two-dimensional integer data.

These structures are particularly useful for managing dimensions, screen coordinates, or grid indices. They include methods for setting and retrieving individual components, such as `SetWidth(tIntType)`, `GetWidth()`, `SetHeight(tIntType)`, and `GetHeight()`. For dynamic layouts, values can be derived from existing floating-point data through methods like `FromFloatVec2(const FLOAT_VECTOR_N_TYPE &)`.

```cpp
Vector2 vector(100.0f, 200.0f);
Uint16Pair pair = Uint16Pair::FromFloatVec2(vector);
uint32_t width = pair.width;
```

## Procedural Math Helpers

The module includes utility namespaces for generating procedural values and performing advanced logarithmic calculations. The `Random` offers tools such as the `Range(float, float)`, which returns a random number within a specified interval, and the `Axis()`, which generates a normalized random direction vector.

For performance-critical code requiring compile-time evaluation, the `Log` and `Power` provide template-based logarithmic and exponential calculations. These utilities allow developers to bake constant values into their applications to reduce runtime computation.

```cpp
float val = Random::Range(0.0f, 1.0f);
Vector4 axis = Random::Axis();
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/math)
