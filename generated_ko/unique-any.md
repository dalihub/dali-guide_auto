---
title: Unique Any
sidebar_label: Unique Any
category: utilities
---

# Unique Any

`UniqueAny`는 단일 값에 대한 타입 소거 저장소를 제공하며, 복사 가능한 타입과 이동 전용 타입을 모두 지원합니다. dali-ui 내부적으로 뷰 첨부 파일에 사용되며 임의의 값을 타입 안전하게 검색하여 저장할 수 있습니다.

## 목차

- [값 저장](#값-저장)
- [값 검색](#값-검색)
- [소유권 이전](#소유권-이전)
- [상태 확인 및 지우기](#상태-확인-및-지우기)

## 값 저장

생성자에 값을 전달하여 `UniqueAny`를 생성합니다. 값은 내부 저장소로 이동되어 이동 전용 타입도 저장할 수 있습니다.

```cpp
// 기본 값 저장
Dali::Ui::UniqueAny attachment(42);

// 이동 전용 타입 저장
struct MoveOnly {
  explicit MoveOnly(int v) : value(v) {}
  MoveOnly(MoveOnly&&) noexcept = default;
  MoveOnly(const MoveOnly&) = delete;
  int value;
};

Dali::Ui::UniqueAny moveAttachment(MoveOnly(7));
```

기본 생성자는 값을 보유하지 않는 빈 `UniqueAny`를 생성합니다:

```cpp
Dali::Ui::UniqueAny empty; // 비어있음, 값 없음
```

## 값 검색

`Get<T>()` 메서드를 사용하여 저장된 값에 대한 포인터를 검색합니다. 요청한 타입이 저장된 타입과 일치하지 않으면 `nullptr`를 반환합니다.

```cpp
Dali::Ui::UniqueAny attachment(3);

int* value = attachment.Get<int>();
if (value) {
  // *value 사용
}

// 타입 불일치 시 nullptr 반환
float* wrong = attachment.Get<float>(); // nullptr
```

반환된 포인터는 `UniqueAny`가 소유합니다. 삭제하지 마시고 `UniqueAny`가 파괴되거나 이동된 후 사용하지 마세요.

## 소유권 이전

`Detach<T>()`를 사용하여 저장된 값의 소유권을 `UniqueAny` 밖으로 이전합니다. 성공하면 `UniqueAny`는 비어있게 되고 소유권이 `Dali::UniquePtr`로 반환됩니다.

```cpp
Dali::Ui::UniqueAny attachment(MoveOnly(17));

// 타입 불일치는 UniqueAny를 변경하지 않음
Dali::UniquePtr<int> mismatch = attachment.Detach<int>();
// mismatch.Get() == nullptr, attachment는 여전히 MoveOnly를 보유

// 올바른 타입은 소유권 이전
Dali::UniquePtr<MoveOnly> value = attachment.Detach<MoveOnly>();
if (value.Get()) {
  // 소유권 이전됨, value->value 사용
}
// attachment는 이제 비어있음
```

## 상태 확인 및 지우기

### 값 보유 확인

`HasValue()`로 `UniqueAny`가 값을 보유하고 있는지 확인합니다:

```cpp
if (attachment.HasValue()) {
  // 값이 저장되어 있음
}
```

### 값 지우기

`Clear()`로 저장된 값을 지웁니다:

```cpp
attachment.Clear();
// attachment.HasValue() == false
```

### 비어있는지 확인

`IsEmpty()`로 비어있는지 확인합니다:

```cpp
if (attachment.IsEmpty()) {
  // 값이 없음
}
```
