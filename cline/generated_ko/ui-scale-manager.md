---
title: Ui 스케일 관리자
sidebar_label: Ui 스케일 관리자
category: styling-theme-config
---

# Ui 스케일 관리자

UiScaleManager는 시스템 기반 적응형 UI 스케일에 대한 접근을 제공하여 애플리케이션이 디스플레이 스케일 변경에 응답하고 개별 뷰가 스케일링에 참여하는 방식을 제어할 수 있게 합니다.

## 목차

- [스케일 관리자 얻기](#스케일-관리자-얻기)
- [시스템 스케일 읽기 및 설정](#시스템-스케일-읽기-및-설정)
- [뷰에 대한 스케일 정책](#뷰에-대한-스케일-정책)

## 스케일 관리자 얻기

`UiScaleManager`는 현재 시스템 스케일 팩터에 대한 접근을 제공하는 싱글톤입니다. 정적 `Get()` 메서드로 싱글톤 인스턴스를 검색합니다.

```cpp
UiScaleManager scaleManager = UiScaleManager::Get();
float currentScale = scaleManager.GetScale();
```

스케일 관리자는 안전하게 복사하거나 이동할 수 있는 핸들 기반 객체입니다:

```cpp
UiScaleManager manager1 = UiScaleManager::Get();
UiScaleManager manager2 = manager1; // 복사 생성자
UiScaleManager manager3 = std::move(manager1); // 이동 생성자
```

## 시스템 스케일 읽기 및 설정

### 현재 스케일 읽기

`GetScale()`로 현재 시스템 스케일 팩터를 검색합니다. 기본값은 1.0입니다.

```cpp
float scale = UiScaleManager::Get().GetScale();
// 적응형 레이아웃 계산에 스케일 값 사용
```

### 시스템 스케일 설정

`SetScale()`로 시스템 스케일 팩터를 변경합니다. 이는 등록된 모든 레이아웃 루트의 재레이아웃을 트리거합니다.

```cpp
void ApplyUserScale(float userScale) {
  if (userScale >= 0.5f && userScale <= 3.0f) {
    UiScaleManager::Get().SetScale(userScale);
  }
}
```

사용자 입력에 응답하는 일반적인 패턴:

```cpp
void OnScalePresetSelected(float presetScale) {
  UiScaleManager::Get().SetScale(presetScale);
  UpdateScaleDisplay();
}

void UpdateScaleDisplay() {
  float currentScale = UiScaleManager::Get().GetScale();
  // 현재 스케일을 표시하도록 UI 업데이트
}
```

## 뷰에 대한 스케일 정책

각 뷰는 `UiScalePolicy`를 통해 시스템 기반 스케일에 참여하는 방식을 제어할 수 있습니다. 정책은 뷰와 그 자손에 대한 유효 스케일이 계산되는 방식을 결정합니다.

### 사용 가능한 정책

| 정책 | 동작 |
|--------|----------|
| `UiScalePolicy::INHERIT` | 부모로부터 유효 스케일 상속. 루트 뷰는 UiScaleManager로부터 상속. 기본값. |
| `UiScalePolicy::ENABLED` | 항상 현재 UiScaleManager 스케일 적용, 부모 정책 무시. |
| `UiScalePolicy::DISABLED` | 시스템 스케일 무시, 스케일 1.0 사용. |

### 뷰에 정책 설정

```cpp
// 이 뷰는 시스템 스케일을 무시
view.SetUiScalePolicy(UiScalePolicy::DISABLED);

// 이 뷰는 항상 시스템 스케일 적용
view.SetUiScalePolicy(UiScalePolicy::ENABLED);
```

### 유효 스케일 조회

```cpp
float effectiveScale = view.GetEffectiveUiScale();
```
