---
title: 상태 이벤트
sidebar_label: 상태 이벤트
category: input-interaction
---

# 상태 이벤트

`StateEvent`는 단일 View 상태 전환을 설명하며 `StateChangedSignal`에 연결된 핸들러에 전달됩니다. 이전 및 현재 `ViewState`와 함께 특정 전환을 감지하는 헬퍼 메서드 및 변경을 유발한 입력 이벤트에 대한 선택적 정보를 제공합니다.

## 목차

- [상태 변경에 응답](#상태-변경에-응답)
- [상태 전환 감지](#상태-전환-감지)
- [원인 이벤트 접근](#원인-이벤트-접근)

## 상태 변경에 응답

View의 상태가 변경될 때 알림을 받으려면 `StateChangedSignal()`에 핸들러를 연결합니다. 핸들러는 `View` 인스턴스와 전환 세부 정보가 포함된 `StateEvent` 객체를 모두 받습니다.

```cpp
view.StateChangedSignal().Connect(&tracker, [](View view, const StateEvent& e) {
  // 상태 변경 처리
  ViewState previous = e.GetPrev();
  ViewState current = e.GetCurrent();
});
```

시그널은 상태가 실제로 변경될 때만 발생합니다. 동일한 상태 값을 두 번 설정하면 단일 알림만 발생합니다.

## 상태 전환 감지

`StateEvent`는 전환 중에 특정 상태가 추가, 제거 또는 변경되었는지 확인하는 헬퍼 메서드를 제공합니다.

### 추가된 상태 확인

`Added()`를 사용하여 상태가 새로 추가되었는지 확인합니다:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View view, const StateEvent& e) {
  if (e.Added(ViewState::FOCUSED)) {
    // 뷰가 키보드 포커스를 얻음
  }
});
```

### 제거된 상태 확인

`Removed()`를 사용하여 상태가 제거되었는지 확인합니다:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View view, const StateEvent& e) {
  if (e.Removed(ViewState::PRESSED)) {
    // 뷰가 눌림 해제됨
  }
});
```

### 변경 확인

`Changed()`를 사용하여 상태가 추가되거나 제거되었는지 확인합니다:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View view, const StateEvent& e) {
  if (e.Changed(ViewState::SELECTED)) {
    // 선택 상태가 변경됨 (추가 또는 제거)
  }
});
```

### 직접 상태 비교

이전 및 현재 상태에 직접 접근할 수 있습니다:

```cpp
ViewState previous = e.GetPrev();
ViewState current = e.GetCurrent();

// 상태 비교
if (previous.Contains(ViewState::FOCUSED) && !current.Contains(ViewState::FOCUSED))
{
  // 포커스를 잃음
}
```

## 원인 이벤트 접근

상태 변경을 유발한 입력 이벤트에 접근합니다:

```cpp
view.StateChangedSignal().Connect(&tracker, [](View view, const StateEvent& e) {
  InputEvent cause = e.GetCauseEvent();
  if (cause)
  {
    // 입력 이벤트로 인한 상태 변경
    Vector2 position = cause.GetPoint();
  }
});
```
