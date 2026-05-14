---
title: 웹 뷰
sidebar_label: 웹 뷰
category: views-components
---

# 웹 뷰

WebView는 dali-ui 애플리케이션 내에서 웹 콘텐츠를 표시하는 View 컴포넌트입니다. 웹 페이지 임베딩을 가능하게 하며 탐색, JavaScript 상호작용, 페이지 로드 모니터링 기능을 제공합니다.

## 목차

- [WebView 생성](#webview-생성)
- [콘텐츠 로드](#콘텐츠-로드)
- [탐색 및 기록](#탐색-및-기록)
- [페이지 로드 모니터링](#페이지-로드-모니터링)
- [JavaScript 상호작용](#javascript-상호작용)
- [스크롤 제어](#스크롤-제어)
- [확대/축소 및 배율](#확대축소-및-배율)
- [스크린샷 및 페이지 정보](#스크린샷-및-페이지-정보)
- [시그널](#시그널)

## WebView 생성

`WebView::New()`로 WebView를 생성합니다. WebView는 View를 상속하므로 표준 View 메서드를 사용하여 크기와 위치를 지정할 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/web-view.h>

using namespace Dali;
using namespace Dali::Ui;

// 빈 WebView 생성
WebView webView = WebView::New();

// URL을 즉시 로드하며 생성
WebView webView = WebView::New(Dali::String("https://www.example.com"));

// WebView 크기 및 위치 지정
webView.SetRequestedWidth(800.0f)
      .SetRequestedHeight(600.0f)
      .SetRequestedPositionX(0.0f)
      .SetRequestedPositionY(0.0f);

// 윈도우에 추가
window.Add(webView);
```

## 콘텐츠 로드

WebView는 웹 콘텐츠를 로드하는 여러 메서드를 제공합니다.

### URL 로드

`LoadUrl()`로 웹 주소로 이동합니다:

```cpp
webView.LoadUrl(Dali::String("https://www.example.com"));
```

### HTML 문자열 로드

`LoadHtmlString()`으로 인라인 HTML 콘텐츠를 표시합니다:

```cpp
webView.LoadHtmlString(Dali::String(
  "<html><body>"
  "<h1>dali-ui에서 인사합니다</h1>"
  "</body></html>"));
```

### 원시 콘텐츠 로드

`LoadContents()`로 지정된 MIME 타입과 인코딩으로 원시 콘텐츠를 로드합니다:

```cpp
const char* htmlContent = "<html><body>테스트</body></html>";
webView.LoadContents(
  Dali::String(htmlContent),
  strlen(htmlContent),
  Dali::String("text/html"),
  Dali::String("UTF-8"),
  Dali::String("https://example.com/"));
```

## 탐색 및 기록

### 뒤로 및 앞으로 탐색

```cpp
// 뒤로 가기
if (webView.CanGoBack())
{
  webView.GoBack();
}

// 앞으로 가기
if (webView.CanGoForward())
{
  webView.GoForward();
}
```

### 기록 지우기

```cpp
webView.ClearHistory();
```

## 페이지 로드 모니터링

페이지 로드 이벤트를 모니터링하려면 시그널에 연결합니다:

```cpp
class MyController : public ConnectionTracker
{
public:
  void SetupWebView(WebView webView)
  {
    webView.PageLoadStartedSignal().Connect(this, &MyController::OnPageLoadStarted);
    webView.PageLoadFinishedSignal().Connect(this, &MyController::OnPageLoadFinished);
  }

  void OnPageLoadStarted(WebView view, const Dali::String& url)
  {
    // 페이지 로드 시작
  }

  void OnPageLoadFinished(WebView view, const Dali::String& url)
  {
    // 페이지 로드 완료
  }
};
```

## JavaScript 상호작용

### JavaScript 실행

`ExecuteJavaScript()`로 JavaScript 코드를 실행합니다:

```cpp
webView.ExecuteJavaScript(Dali::String("alert('Hello from dali-ui!');"));
```

### JavaScript 결과 받기

`ExecuteJavaScriptWithResult()`로 결과를 받습니다:

```cpp
webView.ExecuteJavaScriptWithResult(
  Dali::String("document.title"),
  [](const Dali::String& result) {
    // 결과 처리
  });
```

## 스크롤 제어

```cpp
// 스크롤 가능 여부 확인
bool canScroll = webView.CanScroll();

// 스크롤 위치 가져오기
Vector2 scrollPosition = webView.GetScrollPosition();
Vector2 scrollSize = webView.GetScrollSize();
```

## 확대/축소 및 배율

```cpp
// 페이지 배율 설정
webView.SetPageScaleFactor(1.5f);

// 현재 배율 가져오기
float scale = webView.GetPageScaleFactor();

// 텍스트 배율 설정
webView.SetTextZoomEnabled(true);
webView.SetTextZoomScale(1.2f);
```

## 스크린샷 및 페이지 정보

```cpp
// 스크린샷 캡처
webView.CaptureScreenshot();

// 페이지 제목 가져오기
Dali::String title = webView.GetTitle();

// 현재 URL 가져오기
Dali::String url = webView.GetUrl();
```

## 시그널

WebView는 다음 시그널을 제공합니다:

- `PageLoadStartedSignal()` - 페이지 로드 시작
- `PageLoadFinishedSignal()` - 페이지 로드 완료
- `UrlChangedSignal()` - URL 변경
- `ScreenshotCapturedSignal()` - 스크린샷 캡처 완료
- `JavaScriptAlertSignal()` - JavaScript alert
- `JavaScriptConfirmSignal()` - JavaScript confirm
- `JavaScriptPromptSignal()` - JavaScript prompt
