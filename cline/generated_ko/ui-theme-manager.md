---
title: Ui 테마 관리자
sidebar_label: Ui 테마 관리자
category: styling-theme-config
---

# Ui 테마 관리자

UiThemeManager는 현재 테마 식별자에 대한 접근을 제공하고 테마가 변경될 때 애플리케이션에 알리는 싱글톤입니다.

## 목차

- [테마 관리자 접근](#테마-관리자-접근)
- [현재 테마 가져오기](#현재-테마-가져오기)
- [테마 변경에 응답](#테마-변경에-응답)

## 테마 관리자 접근

`UiThemeManager`는 핸들 기반 싱글톤입니다. 정적 `Get()` 메서드로 인스턴스를 얻습니다:

```cpp
#include <dali-ui-foundation/public-api/ui-theme-manager.h>

using namespace Dali::Ui;

UiThemeManager manager = UiThemeManager::Get();
```

반환된 핸들은 안전하게 복사하거나 이동할 수 있습니다:

```cpp
UiThemeManager manager = UiThemeManager::Get();
// 복사 생성
UiThemeManager copy(manager);
// 이동 생성
UiThemeManager moved(std::move(manager));
```

`BaseHandle`을 `UiThemeManager`로 다운캐스트하려면:

```cpp
BaseHandle handle = UiThemeManager::Get();
UiThemeManager manager = UiThemeManager::DownCast(handle);
if (manager) {
  // 유효한 UiThemeManager
}
```

## 현재 테마 가져오기

`GetCurrentThemeId()`를 호출하여 활성 테마의 식별자를 검색합니다:

```cpp
UiThemeManager manager = UiThemeManager::Get();
String themeId = manager.GetCurrentThemeId();
```

이 메서드는 테마 식별자 문자열을 반환하거나, 활성 테마가 없으면 빈 문자열을 반환합니다. 기본 테마 로더는 커스텀 테마가 설정되지 않았을 때 `"default"`를 반환합니다.

## 테마 변경에 응답

애플리케이션 테마가 변경될 때 반응하려면 `ThemeChangedSignal()`에 연결합니다:

```cpp
#include <dali-ui-foundation/public-api/ui-theme-manager.h>
#include <dali/public-api/signals/connection-tracker.h>

using namespace Dali;
using namespace Dali::Ui;

class ThemeObserver : public ConnectionTracker
{
public:
  ThemeObserver()
  {
    UiThemeManager manager = UiThemeManager::Get();
    manager.ThemeChangedSignal().Connect(this, &ThemeObserver::OnThemeChanged);
  }

  void OnThemeChanged()
  {
    // 테마 변경 처리
    String newTheme = UiThemeManager::Get().GetCurrentThemeId();
    // 새 테마로 UI 업데이트
  }
};
```
