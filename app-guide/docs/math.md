---
id: math
title: "math"
sidebar_label: "math"
---
## Overview

The DALi [math](./math.md) module provides a robust set of geometric primitives and linear algebra utilities designed to handle coordinate transformations, spatial calculations, and visual sizing for UI elements. These tools ensure that developers can perform complex calculations with consistency and precision across various UI components.

## Coordinate and Spatial Representation

The framework utilizes vector classes to define dimensions, positions, and color data. The `Vector2` provides a two-dimensional coordinate system, while the `Vector3` and `Vector4` extend this to three and four dimensions respectively. These vectors support standard arithmetic operations, including addition, subtraction, multiplication, and division, allowing for seamless manipulation of spatial data.

The `Vector2` is primarily used for two-dimensional points and sizes, offering methods such as `Length()` and `Normalize()` for spatial analysis. Similarly, the `Vector3` allows for 3D coordinate management and includes cross-product capabilities through the `Cross(const Vector3 &)`. For applications requiring RGBA color channels or homogeneous coordinates, the `Vector4` provides advanced functionality like the `Dot3(const Vector4 &)` and `Dot4(const Vector4 &)` to perform partial or full dot product calculations.

```cpp
Vector3 myVector(1.0f, 2.0f, 2.0f);
myVector.Normalize();
```

In addition to floating-point vectors, the framework provides integer-based pairs for scenarios requiring discrete grid or pixel values. The `IntPair` and `Uint16Pair` facilitate the handling of integer dimensions, ensuring that integer-based layout properties remain accurate.

## Transformation and Matrix Operations

Transformations, including scaling, translation, and rotation, are handled by the `Matrix` and the `Matrix3`. These classes serve as the backbone for calculating the final visual state of components within the application.

The `Matrix` supports full 4x4 matrix operations, which are essential for rendering pipelines and complex coordinate system transformations. Developers can define transformations using the `SetTransformComponents(const Vector3 &, const Quaternion &, const Vector3 &)`, which encapsulates position, scale, and rotation data into a single matrix. Conversely, the `GetTransformComponents(Vector3 &, Quaternion &, Vector3 &)` allows for the extraction of these properties from an existing matrix. For scenarios requiring non-invertible or simpler 3x3 transformations, the `Matrix3` offers a more lightweight alternative.

```cpp
Matrix myMatrix;
Vector3 scale(1.0f, 1.0f, 1.0f);
Quaternion rotation(Radian(0.0f), Vector3(0.0f, 0.0f, 1.0f));
Vector3 translation(10.0f, 20.0f, 0.0f);
myMatrix.SetTransformComponents(scale, rotation, translation);
```

## Rotation and Orientation Handling

Managing orientation is simplified through the use of quaternions, which provide a stable and artifact-free method for representing rotations in 3D space. The `Quaternion` prevents issues like gimbal lock, which can occur with Euler angle representations.

The framework allows for the creation of quaternions from various sources, including axis-angle pairs through the `AngleAxis`, individual Euler angles, or existing matrices. Smooth animations between two orientations are achieved using the `Slerp(const Quaternion &, const Quaternion &, float)`, which performs spherical linear interpolation, or the `Squad(const Quaternion &, const Quaternion &, const Quaternion &, const Quaternion &, float)` for more complex, cubic-based interpolation paths. The `Rotate(const Vector3 &)` allows for the transformation of vectors based on a given rotation.

```cpp
Quaternion myRotation(Radian(0.5f), Vector3(0.0f, 1.0f, 0.0f));
Vector3 myVector(1.0f, 0.0f, 0.0f);
Vector3 result = myRotation.Rotate(myVector);
```

## Layout Geometry and Bounding Boxes

The `Rect` is a templated structure used to manage rectangular areas within the UI. It provides essential methods for geometric logic, such as determining if a specific point or area lies within another using the `Contains(const Rect< T > &)` or evaluating spatial overlap via the `Intersects(const Rect< T > &)`.

To modify layout constraints dynamically, the `Inset(T, T)` allows developers to shift the boundaries of a rectangle inward or outward. When combining layout elements, the `Merge(const Rect< T > &)` combines two rectangles, ensuring the resulting area encapsulates both original shapes.

```cpp
Rect<int> rectA(0, 0, 100, 100);
Rect<int> rectB(50, 50, 100, 100);
bool isIntersecting = rectA.Intersects(rectB);
```

## Angle and Unit Conversion

Geometry calculations require consistent angular units, which are managed by the `Radian` and the `Degree`. These structures provide explicit types for angular measurements, preventing ambiguity in function signatures.

The `Radian` serves as the standard unit for most mathematical functions, while the `Degree` is available for developer-friendly input. Conversions between these types are handled directly by the constructors and assignment operators, facilitating easy transitions between human-readable degrees and machine-ready radians.

```cpp
Degree myDegree(90.0f);
Radian myRadian(myDegree);
```

## Utility and Randomized Values

The math module includes static utilities to assist with common computational needs, such as precision-based comparison and stochastic value generation. The `Epsilon` provides template-based access to machine epsilon values, enabling reliable floating-point comparisons.

For generating varied UI behavior, the `Random` offers the `Range(float, float)`, which returns a float within specified bounds. Additionally, the `Random::Axis()` can be used to generate a normalized vector in a random direction, which is particularly useful for physics-based effects or particle system initialization.

```cpp
float randomValue = Random::Range(0.0f, 100.0f);
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/math)
