# Animation Prose Review

## Summary

Reviewed the Animation guide draft against public headers in dali-core and dali-ui, sample code, and UTC files. The draft was largely accurate with a few corrections needed.

## Changes Made

### 1. Creating an Animation Section
**Change**: Added documentation for `Animation::New()` without arguments.

**Source Evidence**: `repos/dali-core/dali/public-api/animation/animation.h` line 143-148:
```cpp
/**
 * @brief Creates an initialized Animation.
 * The animation will not loop.
 * The default end action is "Bake".
 * The default alpha function is linear.
 * @SINCE_2_5.18
 * @return A handle to a newly allocated Dali resource
 */
static Animation New();
```

**Reason**: The original draft only mentioned `Animation::New(float durationSeconds)`, but the no-argument overload exists since 2.5.18 and is used in the View-First Animation Pattern examples.

### 2. Key Frame Animation Section
**Change**: Replaced undefined `propertyIndex` variable with concrete `Actor::Property::COLOR`.

**Source Evidence**: `repos/dali-core/dali/public-api/animation/animation.h` shows `AnimateBetween` takes a `Property` target. The example now uses a concrete, animatable property.

**Reason**: The original example used an undefined `propertyIndex` variable, making the code incomplete. Using `Actor::Property::COLOR` with the `Vector4` key frame values provides a complete, working example.

### 3. Duration Literal Section
**Change**: Changed `using Dali::Ui::Duration;` to `using namespace Dali::Ui;`.

**Source Evidence**: `repos/dali-ui/dali-ui-foundation/public-api/animation/duration.h` shows the literal operators are separate functions in the `Dali::Ui` namespace:
```cpp
constexpr Duration operator""_ms(unsigned long long value)
```

**Reason**: `using Dali::Ui::Duration;` only brings in the `Duration` type name, not the `operator""_ms` and `operator""_s` literal operators. To use `_ms` and `_s` literals without qualification, the entire namespace or the specific operators must be imported.

### 4. Added `_s` Literal Documentation
**Change**: Mentioned both `_ms` and `_s` literals in the Duration Literal section.

**Source Evidence**: `repos/dali-ui/dali-ui-foundation/public-api/animation/duration.h` defines both:
```cpp
constexpr Duration operator""_ms(unsigned long long value)
constexpr Duration operator""_s(unsigned long long value)
```

**Reason**: Both literals are available and useful for animation durations.

## Verified Accurate Content

The following were verified against headers and remain unchanged:

- `Animation::New(float)` signature and behavior
- `SetDuration()`, `GetDuration()` methods
- `AnimateTo()` and `AnimateBy()` overloads with `Property`, `AlphaFunction`, and `TimePeriod`
- `TimePeriod` constructor order: `TimePeriod(delaySeconds, durationSeconds)`
- All listed `AlphaFunction::BuiltinFunction` values (LINEAR, EASE_IN, EASE_OUT, EASE_IN_OUT, BOUNCE, SIN)
- Custom bezier `AlphaFunction` constructor with two `Vector2` control points
- `KeyFrames::New()` and `KeyFrames::Add()` methods
- `AnimateBetween()` overloads with `KeyFrames` and `AlphaFunction`
- Playback methods: `Play()`, `Pause()`, `Stop()`, `PlayAfter()`, `PlayFrom()`
- `GetCurrentProgress()`, `SetCurrentProgress()`, `GetState()`
- `Animation::State` enum values: `STOPPED`, `PLAYING`, `PAUSED`
- Looping: `SetLooping()`, `SetLoopCount()`, `GetCurrentLoop()`, `SetLoopingMode()`
- `Animation::LoopingMode` enum: `RESTART`, `AUTO_REVERSE`
- `SetSpeedFactor()`, `SetPlayRange()`
- `FinishedSignal()` connection pattern
- `Animation::EndAction` enum: `BAKE`, `DISCARD`, `BAKE_FINAL`
- `SetEndAction()`, `SetDisconnectAction()`
- `ViewAnimationBridge` fluent API with `Opacity()`, `PositionY()`, etc.
- `ViewAnimationSpec` pattern with `View::NewAnimationSpec()` and `ApplyTo()`

## Remaining Concerns

None. All code examples now match verified API signatures from public headers.
