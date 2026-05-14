---
title: 웹 뷰
sidebar_label: 웹 뷰
category: views-components
---

# 웹 뷰 {#web-view}

`Dali::Ui::WebView`는 `Dali::Ui::View` 객체 모델을 유지하면서 dali-ui 애플리케이션 안에 웹 콘텐츠를 표시합니다.

## 목차 {#table-of-contents}

- [웹 뷰 생성 및 구성](#create-and-configure-a-web-view)
- [페이지와 로컬 콘텐츠 로드](#load-pages-and-local-content)
- [페이지 이동 및 상태 추적](#navigate-and-track-page-state)
- [JavaScript 처리](#handle-javascript)
- [페이지 콘텐츠 캡처](#capture-page-content)
- [입력, 스크롤 및 전체 화면 제어](#control-input-scrolling-and-fullscreen)
- [렌더링, 네트워크 및 미디어 상태 관리](#manage-rendering-network-and-media-state)
- [웹 뷰 지원 타입](#web-view-supporting-types)

## 웹 뷰 생성 및 구성 {#create-and-configure-a-web-view}

`Dali::Ui::WebView::New`로 웹 뷰를 생성합니다. `Dali::Ui::WebView`는 `Dali::Ui::View`이므로, 애플리케이션 코드는 이를 dali-ui 뷰 트리에 유지하면서 원시 속성 호출 대신 타입이 지정된 웹 뷰 setter로 구성할 수 있습니다.

```cpp
Dali::Ui::WebView webView = Dali::Ui::WebView::New();

webView
  .SetUserAgent(Dali::String("MyDaliUiApp/1.0"))
  .SetMouseEventsEnabled(true)
  .SetKeyEventsEnabled(true)
  .SetVideoHoleEnabled(true)
  .SetDocumentBackgroundColor(Dali::Vector4(1.0f, 1.0f, 1.0f, 1.0f))
  .SetPageZoomFactor(1.0f)
  .SetTextZoomFactor(1.0f)
  .SetTilesClearedWhenHidden(true)
  .SetTileCoverAreaMultiplier(2.0f)
  .SetCursorEnabledByClient(true);
```

UI 상태가 현재 구성에 따라 달라지는 경우 대응하는 getter를 사용합니다.

```cpp
const bool mouseEnabled = webView.GetMouseEventsEnabled();
const bool keyEnabled   = webView.GetKeyEventsEnabled();
const bool videoHole    = webView.GetVideoHoleEnabled();

const float pageZoom = webView.GetPageZoomFactor();
const float textZoom = webView.GetTextZoomFactor();
const float scale    = webView.GetScaleFactor();

Dali::String userAgent = webView.GetUserAgent();
```

핸들이 기본 `Dali::Ui::View`로 저장되어 있다면 `Dali::Ui::WebView::DownCast`로 웹 뷰 API를 다시 얻습니다.

```cpp
Dali::Ui::View view = webView;

Dali::Ui::WebView castWebView = Dali::Ui::WebView::DownCast(view);
if(castWebView)
{
  castWebView.Reload();
}
```

## 페이지와 로컬 콘텐츠 로드 {#load-pages-and-local-content}

원격 URL 또는 파일 URL에는 `Dali::Ui::WebView::LoadUrl`을 사용합니다. 애플리케이션이 이미 HTML 문자열을 가지고 있는 경우에는 `Dali::Ui::WebView::LoadHtmlString`을 사용합니다.

```cpp
Dali::Ui::WebView webView = Dali::Ui::WebView::New();

webView.LoadUrl(Dali::String("https://www.example.com"));
```

```cpp
Dali::Ui::WebView webView = Dali::Ui::WebView::New();

webView.LoadHtmlString(Dali::String(
  "<html>"
  "<head><title>Offline</title></head>"
  "<body>"
  "<h1>Offline content</h1>"
  "<p>This page was supplied by the application.</p>"
  "</body>"
  "</html>"));
```

바이트 버퍼의 경우 `Dali::Ui::WebView::LoadContents`는 콘텐츠 포인터, 바이트 크기, MIME 타입, 인코딩, 기본 URI를 받습니다. `Dali::Ui::WebView::LoadHtmlStringOverrideCurrentEntry`는 현재 히스토리 항목을 대체하면서 HTML 문서를 로드합니다.

```cpp
const char html[] =
  "<html><body><h1>Embedded content</h1></body></html>";

const bool loaded = webView.LoadContents(
  reinterpret_cast<const int8_t*>(html),
  static_cast<uint32_t>(sizeof(html) - 1u),
  Dali::String("text/html"),
  Dali::String("UTF-8"),
  Dali::String("https://app.local/"));

if(!loaded)
{
  webView.LoadHtmlString(Dali::String("<html><body>Fallback</body></html>"));
}
```

```cpp
const bool replaced = webView.LoadHtmlStringOverrideCurrentEntry(
  Dali::String("<html><body><h1>Replacement</h1></body></html>"),
  Dali::String("https://app.local/"),
  Dali::String("https://app.local/unreachable"));

if(replaced)
{
  webView.ClearHistory();
}
```

사용자 지정 헤더가 필요한 요청을 로드하기 전에 해당 헤더를 설정합니다.

```cpp
if(webView.AddCustomHeader(Dali::String("X-App-Mode"), Dali::String("embedded")))
{
  webView.LoadUrl(Dali::String("https://www.example.com/dashboard"));
  webView.RemoveCustomHeader(Dali::String("X-App-Mode"));
}
```

## 페이지 이동 및 상태 추적 {#navigate-and-track-page-state}

`Dali::Ui::WebView`는 `Dali::Ui::WebView::GoBack`, `Dali::Ui::WebView::GoForward`, `Dali::Ui::WebView::Reload`, `Dali::Ui::WebView::ReloadWithoutCache`, `Dali::Ui::WebView::StopLoading`, `Dali::Ui::WebView::ClearHistory` 같은 페이지 탐색 명령을 제공합니다.

```cpp
if(webView.CanGoBack())
{
  webView.GoBack();
}
else
{
  webView.LoadUrl(Dali::String("https://www.example.com"));
}

if(webView.CanGoForward())
{
  webView.GoForward();
}

webView.Reload();

const bool reloadStarted = webView.ReloadWithoutCache();
if(!reloadStarted)
{
  webView.StopLoading();
}
```

애플리케이션 UI를 페이지 상태와 동기화하려면 웹 뷰 시그널에 연결합니다. 페이지 로드 및 URL 변경 시그널은 `Dali::Ui::WebView`와 URL을 전달합니다. `Dali::Ui::WebView::FrameRenderedSignal`은 `Dali::Ui::WebView`를 전달합니다.

```cpp
class BrowserController : public Dali::ConnectionTracker
{
public:
  void Attach(Dali::Ui::WebView webView)
  {
    mWebView = webView;

    mWebView.PageLoadStartedSignal().Connect(
      this,
      &BrowserController::OnPageLoadStarted);

    mWebView.PageLoadFinishedSignal().Connect(
      this,
      &BrowserController::OnPageLoadFinished);

    mWebView.UrlChangedSignal().Connect(
      this,
      &BrowserController::OnUrlChanged);

    mWebView.FrameRenderedSignal().Connect(
      this,
      &BrowserController::OnFrameRendered);
  }

private:
  void OnPageLoadStarted(Dali::Ui::WebView view, const Dali::String& url)
  {
    const float progress = view.GetLoadProgressPercentage();
  }

  void OnPageLoadFinished(Dali::Ui::WebView view, const Dali::String& url)
  {
    Dali::String title = view.GetTitle();
    Dali::String currentUrl = view.GetUrl();
  }

  void OnUrlChanged(Dali::Ui::WebView view, const Dali::String& url)
  {
    Dali::String currentUrl = url;
  }

  void OnFrameRendered(Dali::Ui::WebView view)
  {
    Dali::Vector2 contentSize = view.GetContentSize();
  }

  Dali::Ui::WebView mWebView;
};
```

## JavaScript 처리 {#handle-javascript}

애플리케이션에서 스크립트를 실행하려면 `Dali::Ui::WebView::EvaluateJavaScript`를 사용합니다. 콜백 오버로드는 스크립트 결과를 `Dali::String`으로 반환합니다.

```cpp
class ScriptBridge : public Dali::ConnectionTracker
{
public:
  void Attach(Dali::Ui::WebView webView)
  {
    mWebView = webView;
  }

  void ReadTitle()
  {
    mWebView.EvaluateJavaScript(
      Dali::String("document.title"),
      Dali::Ui::WebView::JavaScriptCallback::New(
        this,
        &ScriptBridge::OnJavaScriptResult));
  }

private:
  void OnJavaScriptResult(const Dali::String& result)
  {
    mLastResult = result;
  }

  Dali::Ui::WebView mWebView;
  Dali::String      mLastResult;
};
```

`Dali::Ui::WebView::AddJavaScriptMessageHandler`는 페이지 스크립트가 애플리케이션 측 콜백으로 메시지를 보내는 데 사용할 수 있는 노출 객체 이름을 등록합니다.

```cpp
class MessageBridge : public Dali::ConnectionTracker
{
public:
  void Attach(Dali::Ui::WebView webView)
  {
    mWebView = webView;

    mWebView.AddJavaScriptMessageHandler(
      Dali::String("appBridge"),
      Dali::Ui::WebView::JavaScriptCallback::New(
        this,
        &MessageBridge::OnMessageFromPage));
  }

private:
  void OnMessageFromPage(const Dali::String& message)
  {
    mLastMessage = message;
  }

  Dali::Ui::WebView mWebView;
  Dali::String      mLastMessage;
};
```

JavaScript 대화 상자는 콜백을 등록한 뒤, 대응하는 응답 메서드로 회신하는 방식으로 처리합니다. `Dali::Ui::WebView::RegisterJavaScriptAlertCallback`은 `Dali::Ui::WebView::JavaScriptAlertReply`와 함께 사용하고, `Dali::Ui::WebView::RegisterJavaScriptConfirmCallback`은 `Dali::Ui::WebView::JavaScriptConfirmReply`와 함께 사용하며, `Dali::Ui::WebView::RegisterJavaScriptPromptCallback`은 `Dali::Ui::WebView::JavaScriptPromptReply`와 함께 사용합니다.

```cpp
class DialogBridge : public Dali::ConnectionTracker
{
public:
  void Attach(Dali::Ui::WebView webView)
  {
    mWebView = webView;

    mWebView.RegisterJavaScriptAlertCallback(
      Dali::Ui::WebView::JavaScriptAlertCallback::New(
        this,
        &DialogBridge::OnAlert));

    mWebView.RegisterJavaScriptConfirmCallback(
      Dali::Ui::WebView::JavaScriptConfirmCallback::New(
        this,
        &DialogBridge::OnConfirm));

    mWebView.RegisterJavaScriptPromptCallback(
      Dali::Ui::WebView::JavaScriptPromptCallback::New(
        this,
        &DialogBridge::OnPrompt));
  }

private:
  bool OnAlert(const Dali::String& message)
  {
    mWebView.JavaScriptAlertReply();
    return true;
  }

  bool OnConfirm(const Dali::String& message)
  {
    mWebView.JavaScriptConfirmReply(true);
    return true;
  }

  bool OnPrompt(const Dali::String& message, const Dali::String& defaultValue)
  {
    mWebView.JavaScriptPromptReply(defaultValue);
    return true;
  }

  Dali::Ui::WebView mWebView;
};
```

## 페이지 콘텐츠 캡처 {#capture-page-content}

`Dali::Ui::WebView::GetScreenshot`은 요청한 사각형 영역과 스케일에 대한 `Dali::Ui::ImageView`를 반환합니다. 캡처 작업을 콜백으로 완료해야 하는 경우에는 `Dali::Ui::WebView::GetScreenshotAsynchronously`를 사용합니다.

```cpp
Dali::BoundsInteger area(0, 0, 320, 240);

Dali::Ui::ImageView screenshot = webView.GetScreenshot(area, 1.0f);
```

```cpp
class CaptureController : public Dali::ConnectionTracker
{
public:
  void Attach(Dali::Ui::WebView webView)
  {
    mWebView = webView;
  }

  void Capture()
  {
    Dali::BoundsInteger area(0, 0, 320, 240);

    const bool started = mWebView.GetScreenshotAsynchronously(
      area,
      1.0f,
      Dali::Ui::WebView::ScreenshotCapturedCallback::New(
        this,
        &CaptureController::OnScreenshotCaptured));

    if(!started)
    {
      mFallbackImage = mWebView.GetScreenshot(area, 1.0f);
    }
  }

private:
  void OnScreenshotCaptured(Dali::Ui::ImageView screenshot)
  {
    mLastScreenshot = screenshot;
  }

  Dali::Ui::WebView  mWebView;
  Dali::Ui::ImageView mLastScreenshot;
  Dali::Ui::ImageView mFallbackImage;
};
```

원시 페이지를 직접 검사하지 않고 주변 앱 크롬을 업데이트하려면 페이지 정보 API를 사용합니다.

```cpp
Dali::String title        = webView.GetTitle();
Dali::String url          = webView.GetUrl();
Dali::String selectedText = webView.GetSelectedText();

Dali::Vector2 scrollPosition = webView.GetScrollPosition();
Dali::Vector2 scrollSize     = webView.GetScrollSize();
Dali::Vector2 contentSize    = webView.GetContentSize();

Dali::Ui::ImageView favicon = webView.GetFavicon();
```

일반 텍스트 추출은 비동기 방식으로 수행됩니다.

```cpp
class TextExtractor : public Dali::ConnectionTracker
{
public:
  void Attach(Dali::Ui::WebView webView)
  {
    mWebView = webView;
  }

  void RequestText()
  {
    mWebView.GetPlainTextAsynchronously(
      Dali::Ui::WebView::PlainTextCallback::New(
        this,
        &TextExtractor::OnPlainText));
  }

private:
  void OnPlainText(const Dali::String& text)
  {
    mText = text;
  }

  Dali::Ui::WebView mWebView;
  Dali::String      mText;
};
```

## 입력, 스크롤 및 전체 화면 제어 {#control-input-scrolling-and-fullscreen}

`Dali::Ui::WebView`는 타입이 지정된 setter와 getter를 통해 입력 활성화 상태를 노출합니다.

```cpp
webView
  .SetMouseEventsEnabled(true)
  .SetKeyEventsEnabled(true)
  .SetCursorEnabledByClient(true);

if(webView.GetMouseEventsEnabled())
{
  webView.FeedMouseWheel(true, 1, 160, 120);
}
```

일반적인 스크롤 델타에는 `Dali::Ui::WebView::ScrollBy`를 사용하고, 가장자리를 고려한 스크롤이 필요한 경우에는 `Dali::Ui::WebView::ScrollEdgeBy`를 사용합니다.

```cpp
webView.ScrollBy(0, 120);

const bool edgeScrolled = webView.ScrollEdgeBy(0, 80);
if(!edgeScrolled)
{
  Dali::Vector2 position = webView.GetScrollPosition();
}
```

스크롤 및 전체 화면 이벤트는 웹 뷰 시그널을 통해 전달됩니다. `Dali::Ui::WebView::OverScrolledSignal`은 `Dali::Ui::WebViewOverScrolled`를 사용하고, `Dali::Ui::WebView::ScrollEdgeReachedSignal`은 `Dali::Ui::WebViewScrollEdge`를 사용합니다.

```cpp
class WebInteractionController : public Dali::ConnectionTracker
{
public:
  void Attach(Dali::Ui::WebView webView)
  {
    mWebView = webView;

    mWebView.OverScrolledSignal().Connect(
      this,
      &WebInteractionController::OnOverScrolled);

    mWebView.ScrollEdgeReachedSignal().Connect(
      this,
      &WebInteractionController::OnScrollEdgeReached);

    mWebView.FullscreenEnteredSignal().Connect(
      this,
      &WebInteractionController::OnFullscreenEntered);

    mWebView.FullscreenExitedSignal().Connect(
      this,
      &WebInteractionController::OnFullscreenExited);
  }

private:
  void OnOverScrolled(Dali::Ui::WebView view, Dali::Ui::WebViewOverScrolled direction)
  {
    if(direction == Dali::Ui::WebViewOverScrolled::BOTTOM)
    {
      view.ScrollBy(0, -40);
    }
  }

  void OnScrollEdgeReached(Dali::Ui::WebView view, Dali::Ui::WebViewScrollEdge edge)
  {
    if(edge == Dali::Ui::WebViewScrollEdge::LEFT && view.CanGoBack())
    {
      view.GoBack();
    }
  }

  void OnFullscreenEntered(Dali::Ui::WebView view)
  {
    mFullscreen = true;
  }

  void OnFullscreenExited(Dali::Ui::WebView view)
  {
    mFullscreen = false;
  }

  Dali::Ui::WebView mWebView;
  bool              mFullscreen{false};
};
```

애플리케이션에서 전체 화면을 종료하려면 `Dali::Ui::WebView::ExitFullscreen`을 호출합니다.

```cpp
webView.ExitFullscreen();
```

## 렌더링, 네트워크 및 미디어 상태 관리 {#manage-rendering-network-and-media-state}

웹 뷰 수명 주기에는 `Dali::Ui::WebView::Suspend`와 `Dali::Ui::WebView::Resume`을 사용하고, 네트워크 활동만 일시 중지하거나 재개해야 하는 경우에는 `Dali::Ui::WebView::SuspendNetworkLoading` 또는 `Dali::Ui::WebView::ResumeNetworkLoading`을 사용합니다.

```cpp
void OnAppPaused(Dali::Ui::WebView webView)
{
  webView.SuspendNetworkLoading();
  webView.Suspend();
}

void OnAppResumed(Dali::Ui::WebView webView)
{
  webView.Resume();
  webView.ResumeNetworkLoading();
}
```

타일과 스케일 제어는 `Dali::Ui::WebView`가 담당합니다. 주변 앱이 페이지 표시 방식이나 표시 여부를 변경할 때 유용합니다.

```cpp
webView
  .SetTilesClearedWhenHidden(true)
  .SetTileCoverAreaMultiplier(2.0f);

webView.SetScaleFactor(1.25f, Dali::Vector2(120.0f, 160.0f));

const float scale = webView.GetScaleFactor();

webView.ClearAllTilesResources();
```

비디오 상태는 `Dali::Ui::WebView::CheckVideoPlayingAsynchronously`로 비동기 확인합니다.

```cpp
class MediaStateController : public Dali::ConnectionTracker
{
public:
  void Attach(Dali::Ui::WebView webView)
  {
    mWebView = webView;
    mWebView.SetVideoHoleEnabled(true);
  }

  void RefreshVideoState()
  {
    mWebView.CheckVideoPlayingAsynchronously(
      Dali::Ui::WebView::VideoPlayingCallback::New(
        this,
        &MediaStateController::OnVideoPlaying));
  }

private:
  void OnVideoPlaying(bool playing)
  {
    mVideoPlaying = playing;
  }

  Dali::Ui::WebView mWebView;
  bool              mVideoPlaying{false};
};
```

지리적 위치 권한 요청과 웹 프로세스 크래시는 해당 웹 뷰가 제공하는 시그널을 통해 처리합니다.

```cpp
class PermissionAndRecoveryController : public Dali::ConnectionTracker
{
public:
  void Attach(Dali::Ui::WebView webView)
  {
    mWebView = webView;

    mWebView.GeolocationPermissionSignal().Connect(
      this,
      &PermissionAndRecoveryController::OnGeolocationPermission);

    mWebView.WebProcessCrashedSignal().Connect(
      this,
      &PermissionAndRecoveryController::OnWebProcessCrashed);
  }

private:
  bool OnGeolocationPermission(
    Dali::Ui::WebView view,
    const Dali::String& host,
    const Dali::String& protocol)
  {
    return protocol == Dali::String("https");
  }

  void OnWebProcessCrashed(Dali::Ui::WebView view)
  {
    view.ReloadWithoutCache();
  }

  Dali::Ui::WebView mWebView;
};
```

## 웹 뷰 지원 타입 {#web-view-supporting-types}

`Dali::Ui::WebViewOverScrolled`는 `Dali::Ui::WebView::OverScrolledSignal`이 보고하는 오버 스크롤 방향을 설명합니다. 시그널 핸들러에서는 `Dali::Ui::WebViewOverScrolled::LEFT`, `Dali::Ui::WebViewOverScrolled::BOTTOM` 같은 값을 사용합니다.

```cpp
webView.OverScrolledSignal().Connect(
  [](Dali::Ui::WebView view, Dali::Ui::WebViewOverScrolled direction)
  {
    if(direction == Dali::Ui::WebViewOverScrolled::LEFT)
    {
      view.GoBack();
    }
    else if(direction == Dali::Ui::WebViewOverScrolled::BOTTOM)
    {
      view.ScrollBy(0, -80);
    }
  });
```

`Dali::Ui::WebViewScrollEdge`는 `Dali::Ui::WebView::ScrollEdgeReachedSignal`이 보고하는 스크롤 가장자리를 설명합니다. `Dali::Ui::WebViewScrollEdge::LEFT`, `Dali::Ui::WebViewScrollEdge::RIGHT`, `Dali::Ui::WebViewScrollEdge::TOP`, `Dali::Ui::WebViewScrollEdge::BOTTOM` 같은 값을 사용합니다.

```cpp
webView.ScrollEdgeReachedSignal().Connect(
  [](Dali::Ui::WebView view, Dali::Ui::WebViewScrollEdge edge)
  {
    if(edge == Dali::Ui::WebViewScrollEdge::LEFT && view.CanGoBack())
    {
      view.GoBack();
    }
  });
```

`Dali::Ui::WebViewFindOption`은 `Dali::Ui::WebViewFindOption::NONE`, `Dali::Ui::WebViewFindOption::CASE_INSENSITIVE`, `Dali::Ui::WebViewFindOption::AT_WORD_STARTS`, `Dali::Ui::WebViewFindOption::TREAT_MEDIAL_CAPITAL_AS_WORD_START`, `Dali::Ui::WebViewFindOption::BACKWARDS`, `Dali::Ui::WebViewFindOption::WRAP_AROUND`, `Dali::Ui::WebViewFindOption::SHOW_OVERLAY`, `Dali::Ui::WebViewFindOption::SHOW_FIND_INDICATOR`, `Dali::Ui::WebViewFindOption::SHOW_HIGHLIGHT`처럼 조합 가능한 텍스트 검색 플래그를 정의합니다. 현재 공개 `Dali::Ui::WebView` 헤더는 `Dali::Ui::WebView::TextFoundSignal`을 통해 텍스트 검색 결과를 전달하도록 노출합니다.

```cpp
webView.TextFoundSignal().Connect(
  [](Dali::Ui::WebView view, uint32_t matchCount)
  {
    if(matchCount == 0u)
    {
      view.GetPlainTextAsynchronously(
        Dali::Ui::WebView::PlainTextCallback::New(
          [](const Dali::String& text)
          {
          }));
    }
  });
```
