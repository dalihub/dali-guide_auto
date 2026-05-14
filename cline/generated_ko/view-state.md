---
title: 뷰 상태
sidebar_label: 뷰 상태
category: views-components
---

# 뷰 상태

`ViewState`는 `View`의 인터랙티브 상태를 조합 가능한 비트마스크로 나타냅니다. 뷰는 `NORMAL`, `FOCUSED`, `PRESSED`, `SELECTED`, `DISABLED`와 같은 상태를 사용하여 시각적 피드백과 동작을 구동합니다.

## 목차

- [뷰 상태 이해](#뷰-상태-이해)
- [상태 확인 및 조합](#상태-확인-및-조합)
- [상태 전환 감지](#상태-전환-감지)
- [상태 변경에 응답](#상태-변경에-응답)
- [커스텀 상태](#커스텀-상태)

## 뷰 상태 이해

`ViewState`는 0개 이상의 상태 플래그를 비트마스크로 나타내는 가벼운 값 타입입니다. 기본 생성된 상태는 `NORMAL`이며, 설정된 비트가 없습니다:

```cpp
ViewState state;  // 기본적으로 Normal
if (state.IsNormal()) {
  // 상태에 플래그가 설정되지 않음
}
```

`ViewState`는 미리 정의된 단일 상태 상수를 제공합니다:

| 상수 | 의미 |
|----------|---------|
| `ViewState::NORMAL` | 플래그가 없는 기본 상태 (0 비트) |
| `ViewState::FOCUSED` | 뷰가 입력 포커스를 가짐 |
| `ViewState::PRESSED` | 뷰가 눌림 (터치 또는 키) |
| `ViewState::SELECTED` | 뷰가 선택 상태 |
| `ViewState::DISABLED` | 뷰가 비활성화되어 인터랙티브하지 않음 |
| `ViewState::PSEUDO_DISABLED` | 뷰가 비활성화된 것처럼 보이지만 인터랙티브 유지 |

복합 상태는 여러 플래그를 결합합니다:

```cpp
ViewState::SELECTED_PRESSED   // SELECTED + PRESSED
ViewState::SELECTED_FOCUSED    // SELECTED + FOCUSED
ViewState::DISABLED_SELECTED   // DISABLED + SELECTED
```

## 상태 확인 및 조합

### 상태 내용 테스트

`Contains()`를 사용하여 상태가 특정 플래그를 포함하는지 확인합니다:

```cpp
ViewState state = ViewState::FOCUSED + ViewState::PRESSED;

if (state.Contains(ViewState::FOCUSED)) {
  // Focused 플래그가 설정됨
}

if (state.Contains(ViewState::DISABLED)) {
  // Disabled 플래그가 설정됨
}
```

`IsNormal()`은 0 비트 상태에만 true를 반환합니다:

```cpp
if (ViewState::NORMAL.IsNormal()) {
  // 참 — 플래그가 없음
}

if (ViewState::FOCUSED.IsNormal()) {
  // 거짓 — FOCUSED에 비트가 설정됨
}
```

`IsAnyDisabled()`는 `DISABLED` 또는 `PSEUDO_DISABLED`를 확인합니다:

```cpp
if (state.IsAnyDisabled()) {
  // DISABLED 또는 PSEUDO_DISABLED가 설정됨
}
```

### 상태 조합

`+` 연산자로 상태를 결합합니다:

```cpp
ViewState focusedPressed = ViewState::FOCUSED + ViewState::PRESSED;
```

`-` 연산자로 플래그를 제거합니다:

```cpp
ViewState state = ViewState::FOCUSED + ViewState::PRESSED;
ViewState justFocused = state - ViewState::PRESSED;
```

## 상태 전환 감지

`StateEvent` 클래스는 상태 변경에 대한 세부 정보를 제공합니다.

### 추가 및 제거된 상태 확인

```cpp
void OnStateChanged(View view, StateEvent event)
{
  if (event.Added(ViewState::FOCUSED)) {
    // 뷰가 포커스를 얻음
  }
  if (event.Removed(ViewState::PRESSED)) {
    // 뷰가 눌림 상태를 잃음
  }
}
```

### 상태 변경 시그널

`StateChangedSignal()`에 연결하여 상태 전환에 반응합니다:

```cpp
view.StateChangedSignal().Connect(this, &MyClass::OnStateChanged);
```

## 상태 변경에 응답

일반적인 패턴은 상태 변경에 따라 뷰 외관을 업데이트하는 것입니다:

```cpp
void OnStateChanged(View view, StateEvent event)
{
  if (event.Added(ViewState::FOCUSED)) {
    view.SetBackgroundColor(UiColor::PRIMARY);
  }
  if (event.Removed(ViewState::FOCUSED)) {
    view.SetBackgroundColor(UiColor::SURFACE);
  }
  if (event.Added(ViewState::PRESSED)) {
    view.SetBackgroundColor(UiColor::EMPHASIS);
  }
}
```

## 커스텀 상태

애플리케이션은 `ViewState::USER1`부터 `ViewState::USER8`까지의 커스텀 상태 플래그를 정의할 수 있습니다:

```cpp
// 커스텀 상태 정의
const ViewState CUSTOM_HIGHLIGHT = ViewState::USER1;

// 커스텀 상태 사용
ViewState state = ViewState::FOCUSED + CUSTOM_HIGHLIGHT;
if (state.Contains(CUSTOM_HIGHLIGHT)) {
  // 커스텀 하이라이트 상태
}
```
