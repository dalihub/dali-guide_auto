---
title: Web View
sidebar_label: Web View
category: views-components
---

# Web View

`Dali::Ui::WebView` embeds web content in a dali-ui application as a `Dali::Ui::View`.

## Table of Contents

- [Create and Configure a Web View](#create-and-configure-a-web-view)
- [Load Content and Control Navigation](#load-content-and-control-navigation)
- [Track Page State with Signals](#track-page-state-with-signals)
- [JavaScript Integration](#javascript-integration)
- [Scrolling, Zoom, and Input](#scrolling-zoom-and-input)
- [Page Information, Screenshots, and Media](#page-information-screenshots-and-media)
- [Fullscreen, Geolocation, and Process Events](#fullscreen-geolocation-and-process-events)
- [Supporting Web View Types](#supporting-web-view-types)

## Create and Configure a Web View

Create a `Dali::Ui::WebView` with `Dali::Ui::WebView::New`. The object is a dali-ui `Dali::Ui::View`, so application code should treat it as the app-facing view object and configure it with typed `Dali::Ui::WebView` setters.

```cpp
#include <dali-ui-foundation/public-api/web-view.h>

using namespace Dali;
using namespace Dali::Ui;

WebView CreateConfiguredWebView()
{
  WebView webView = WebView::New();

  webView
    .SetUserAgent(Dali::String("MyDaliUiApp/1.0"))
    .SetDocumentBackgroundColor(Dali::Vector4(1.0f, 1.0f, 1.0f, 1.0f))
    .SetMouseEventsEnabled(true)
    .SetKeyEventsEnabled(true)
    .SetVideoHoleEnabled(true)
    .SetTilesClearedWhenHidden(true)
    .SetTileCoverAreaMultiplier(2.0f)
    .SetCursorEnabledByClient(true);

  return webView;
}
```

Use `Dali::Ui::WebView::GetUserAgent`, `Dali::Ui::WebView::GetMouseEventsEnabled`, `Dali::Ui::WebView::GetKeyEventsEnabled`, and `Dali::Ui::WebView::GetVideoHoleEnabled` when application logic needs to inspect the current configuration.

```cpp
void ReadWebViewConfiguration(WebView webView)
{
  Dali::String userAgent = webView.GetUserAgent();
  bool mouseEnabled      = webView.GetMouseEventsEnabled();
  bool keyEnabled        = webView.GetKeyEventsEnabled();
  bool videoHoleEnabled  = webView.GetVideoHoleEnabled();

  (void)userAgent;
  (void)mouseEnabled;
  (void)keyEnabled;
  (void)videoHoleEnabled;
}
```

If a view handle is passed through generic dali-ui view code, recover the concrete web view with `Dali::Ui::WebView::DownCast`.

```cpp
WebView AsWebView(Dali::Ui::View view)
{
  return WebView::DownCast(view);
}
```

## Load Content and Control Navigation

`Dali::Ui::WebView::LoadUrl` loads a URL. `Dali::Ui::WebView::LoadHtmlString` loads an inline HTML document. Both return `Dali::Ui::WebView&`, so they can be used in fluent view setup code.

```cpp
WebView CreateWebPage()
{
  WebView webView = WebView::New();

  webView
    .SetUserAgent(Dali::String("MyDaliUiApp/1.0"))
    .LoadUrl(Dali::String("https://www.example.com"));

  return webView;
}
```

For local or generated HTML, pass a complete HTML string to `Dali::Ui::WebView::LoadHtmlString`.

```cpp
void ShowOfflinePage(WebView webView)
{
  webView.LoadHtmlString(Dali::String(
    "<html>"
    "<head><title>Offline</title></head>"
    "<body><h1>Offline content</h1><p>This page was loaded from a string.</p></body>"
    "</html>"));
}
```

Use `Dali::Ui::WebView::CanGoBack` and `Dali::Ui::WebView::CanGoForward` before calling `Dali::Ui::WebView::GoBack` or `Dali::Ui::WebView::GoForward`. `Dali::Ui::WebView::Reload`, `Dali::Ui::WebView::ReloadWithoutCache`, `Dali::Ui::WebView::StopLoading`, and `Dali::Ui::WebView::ClearHistory` cover the common browser-style controls.

```cpp
void HandleNavigationCommand(WebView webView, const Dali::String& command)
{
  if(command == "back" && webView.CanGoBack())
  {
    webView.GoBack();
  }
  else if(command == "forward" && webView.CanGoForward())
  {
    webView.GoForward();
  }
  else if(command == "reload")
  {
    webView.Reload();
  }
  else if(command == "reloadWithoutCache")
  {
    bool started = webView.ReloadWithoutCache();
    (void)started;
  }
  else if(command == "stop")
  {
    webView.StopLoading();
  }
}
```

When content is already available in memory, `Dali::Ui::WebView::LoadContents` accepts raw bytes with MIME type, encoding, and base URI. `Dali::Ui::WebView::LoadHtmlStringOverrideCurrentEntry` loads HTML while replacing the current history entry.

```cpp
void LoadGeneratedContent(WebView webView)
{
  const char html[] =
    "<html><head><title>Generated</title></head>"
    "<body><p>Generated document</p></body></html>";

  bool loaded = webView.LoadContents(
    reinterpret_cast<const int8_t*>(html),
    static_cast<uint32_t>(sizeof(html) - 1u),
    Dali::String("text/html"),
    Dali::String("UTF-8"),
    Dali::String("https://app.local/"));

  (void)loaded;
}
```

Custom request headers can be added and removed with `Dali::Ui::WebView::AddCustomHeader` and `Dali::Ui::WebView::RemoveCustomHeader`.

```cpp
void ConfigureRequestHeaders(WebView webView)
{
  bool added = webView.AddCustomHeader(
    Dali::String("X-App-Mode"),
    Dali::String("preview"));

  bool removed = webView.RemoveCustomHeader(Dali::String("X-App-Mode"));

  (void)added;
  (void)removed;
}
```

## Track Page State with Signals

Use `Dali::Ui::WebView::PageLoadStartedSignal`, `Dali::Ui::WebView::PageLoadInProgressSignal`, and `Dali::Ui::WebView::PageLoadFinishedSignal` to update app UI around page loading. Their callbacks receive the `Dali::Ui::WebView` and the URL string.

```cpp
class WebPageController : public Dali::ConnectionTracker
{
public:
  void Attach(WebView webView)
  {
    mWebView = webView;

    mWebView.PageLoadStartedSignal().Connect(
      this,
      &WebPageController::OnPageLoadStarted);

    mWebView.PageLoadFinishedSignal().Connect(
      this,
      &WebPageController::OnPageLoadFinished);
  }

private:
  void OnPageLoadStarted(WebView webView, const Dali::String& url)
  {
    float progress = webView.GetLoadProgressPercentage();
    (void)url;
    (void)progress;
  }

  void OnPageLoadFinished(WebView webView, const Dali::String& url)
  {
    Dali::String title = webView.GetTitle();
    Dali::String finalUrl = webView.GetUrl();

    (void)url;
    (void)title;
    (void)finalUrl;
  }

  WebView mWebView;
};
```

`Dali::Ui::WebView::UrlChangedSignal` is useful when the visible page changes without a full app-level navigation command. `Dali::Ui::WebView::FrameRenderedSignal` fires when a frame is rendered.

```cpp
class WebStateController : public Dali::ConnectionTracker
{
public:
  void Attach(WebView webView)
  {
    webView.UrlChangedSignal().Connect(this, &WebStateController::OnUrlChanged);
    webView.FrameRenderedSignal().Connect(this, &WebStateController::OnFrameRendered);
  }

private:
  void OnUrlChanged(WebView webView, const Dali::String& url)
  {
    Dali::String currentUrl = webView.GetUrl();
    (void)url;
    (void)currentUrl;
  }

  void OnFrameRendered(WebView webView)
  {
    Dali::Vector2 contentSize = webView.GetContentSize();
    (void)contentSize;
  }
};
```

## JavaScript Integration

`Dali::Ui::WebView::EvaluateJavaScript` runs JavaScript in the loaded page. Use the callback overload when the expression should return a result.

```cpp
class ScriptController
{
public:
  void ReadDocumentTitle(WebView webView)
  {
    webView.EvaluateJavaScript(
      Dali::String("document.title"),
      WebView::JavaScriptCallback::New(this, &ScriptController::OnJavaScriptResult));
  }

  void SetDocumentFlag(WebView webView)
  {
    webView.EvaluateJavaScript(Dali::String("window.daliUiReady = true;"));
  }

private:
  void OnJavaScriptResult(const Dali::String& result)
  {
    mLastResult = result;
  }

  Dali::String mLastResult;
};
```

Use `Dali::Ui::WebView::AddJavaScriptMessageHandler` to expose a named message handler to page script. The callback receives the message payload as a `Dali::String`.

```cpp
class WebMessageController
{
public:
  void Attach(WebView webView)
  {
    webView.AddJavaScriptMessageHandler(
      Dali::String("appBridge"),
      WebView::JavaScriptCallback::New(this, &WebMessageController::OnMessage));
  }

private:
  void OnMessage(const Dali::String& message)
  {
    mLastMessage = message;
  }

  Dali::String mLastMessage;
};
```

Dialog callbacks are registered separately. `Dali::Ui::WebView::RegisterJavaScriptAlertCallback`, `Dali::Ui::WebView::RegisterJavaScriptConfirmCallback`, and `Dali::Ui::WebView::RegisterJavaScriptPromptCallback` let app code decide how to handle JavaScript dialogs. Reply with `Dali::Ui::WebView::JavaScriptAlertReply`, `Dali::Ui::WebView::JavaScriptConfirmReply`, or `Dali::Ui::WebView::JavaScriptPromptReply`.

```cpp
class JavaScriptDialogController
{
public:
  void Attach(WebView webView)
  {
    mWebView = webView;

    mWebView.RegisterJavaScriptAlertCallback(
      WebView::JavaScriptAlertCallback::New(this, &JavaScriptDialogController::OnAlert));

    mWebView.RegisterJavaScriptConfirmCallback(
      WebView::JavaScriptConfirmCallback::New(this, &JavaScriptDialogController::OnConfirm));

    mWebView.RegisterJavaScriptPromptCallback(
      WebView::JavaScriptPromptCallback::New(this, &JavaScriptDialogController::OnPrompt));
  }

private:
  bool OnAlert(const Dali::String& message)
  {
    (void)message;
    mWebView.JavaScriptAlertReply();
    return true;
  }

  bool OnConfirm(const Dali::String& message)
  {
    (void)message;
    mWebView.JavaScriptConfirmReply(true);
    return true;
  }

  bool OnPrompt(const Dali::String& message, const Dali::String& defaultValue)
  {
    (void)message;
    mWebView.JavaScriptPromptReply(defaultValue);
    return true;
  }

  WebView mWebView;
};
```

## Scrolling, Zoom, and Input

Use `Dali::Ui::WebView::ScrollBy` for regular scroll deltas and `Dali::Ui::WebView::ScrollEdgeBy` when edge-aware scrolling is needed. Query `Dali::Ui::WebView::GetScrollPosition`, `Dali::Ui::WebView::GetScrollSize`, and `Dali::Ui::WebView::GetContentSize` to synchronize app UI with web content.

```cpp
void ScrollDocument(WebView webView)
{
  webView.ScrollBy(0, 120);

  bool edgeHandled = webView.ScrollEdgeBy(0, 60);

  Dali::Vector2 position = webView.GetScrollPosition();
  Dali::Vector2 scrollSize = webView.GetScrollSize();
  Dali::Vector2 contentSize = webView.GetContentSize();

  (void)edgeHandled;
  (void)position;
  (void)scrollSize;
  (void)contentSize;
}
```

`Dali::Ui::WebView::OverScrolledSignal` reports over-scroll direction with `Dali::Ui::WebViewOverScrolled`. `Dali::Ui::WebView::ScrollEdgeReachedSignal` reports edge reach events.

```cpp
class ScrollController : public Dali::ConnectionTracker
{
public:
  void Attach(WebView webView)
  {
    webView.OverScrolledSignal().Connect(this, &ScrollController::OnOverScrolled);
  }

private:
  void OnOverScrolled(WebView webView, WebViewOverScrolled direction)
  {
    if(direction == WebViewOverScrolled::LEFT)
    {
      webView.ScrollBy(-20, 0);
    }
    else if(direction == WebViewOverScrolled::BOTTOM)
    {
      webView.ScrollBy(0, 20);
    }
  }
};
```

For zoom, `Dali::Ui::WebView::SetPageZoomFactor` changes page zoom, `Dali::Ui::WebView::SetTextZoomFactor` changes text zoom, and `Dali::Ui::WebView::SetScaleFactor` scales around a point. Read back with `Dali::Ui::WebView::GetPageZoomFactor`, `Dali::Ui::WebView::GetTextZoomFactor`, and `Dali::Ui::WebView::GetScaleFactor`.

```cpp
void ConfigureZoom(WebView webView)
{
  webView
    .SetPageZoomFactor(1.25f)
    .SetTextZoomFactor(1.10f);

  webView.SetScaleFactor(2.0f, Dali::Vector2(160.0f, 240.0f));

  float pageZoom = webView.GetPageZoomFactor();
  float textZoom = webView.GetTextZoomFactor();
  float scale    = webView.GetScaleFactor();

  (void)pageZoom;
  (void)textZoom;
  (void)scale;
}
```

Mouse and keyboard delivery can be toggled with `Dali::Ui::WebView::SetMouseEventsEnabled` and `Dali::Ui::WebView::SetKeyEventsEnabled`. `Dali::Ui::WebView::FeedMouseWheel` sends a wheel step at a point in the web view.

```cpp
void ConfigureInput(WebView webView)
{
  webView
    .SetMouseEventsEnabled(true)
    .SetKeyEventsEnabled(true);

  webView.FeedMouseWheel(true, 1, 240, 320);
}
```

## Page Information, Screenshots, and Media

`Dali::Ui::WebView::GetTitle`, `Dali::Ui::WebView::GetUrl`, `Dali::Ui::WebView::GetFavicon`, and `Dali::Ui::WebView::GetSelectedText` expose page state to the app.

```cpp
void ReadPageInfo(WebView webView)
{
  Dali::String title        = webView.GetTitle();
  Dali::String url          = webView.GetUrl();
  Dali::Ui::ImageView icon  = webView.GetFavicon();
  Dali::String selectedText = webView.GetSelectedText();

  (void)title;
  (void)url;
  (void)icon;
  (void)selectedText;
}
```

Use `Dali::Ui::WebView::GetPlainTextAsynchronously` when plain text extraction should return through a callback.

```cpp
class PlainTextReader
{
public:
  void RequestText(WebView webView)
  {
    webView.GetPlainTextAsynchronously(
      WebView::PlainTextCallback::New(this, &PlainTextReader::OnPlainText));
  }

private:
  void OnPlainText(const Dali::String& text)
  {
    mText = text;
  }

  Dali::String mText;
};
```

`Dali::Ui::WebView::GetScreenshot` returns a `Dali::Ui::ImageView` immediately. `Dali::Ui::WebView::GetScreenshotAsynchronously` starts an asynchronous capture and delivers the result to a callback.

```cpp
class ScreenshotController
{
public:
  void Capture(WebView webView)
  {
    Dali::Rect<int32_t> area(0, 0, 320, 240);

    Dali::Ui::ImageView immediate = webView.GetScreenshot(area, 1.0f);
    (void)immediate;

    bool started = webView.GetScreenshotAsynchronously(
      area,
      1.0f,
      WebView::ScreenshotCapturedCallback::New(this, &ScreenshotController::OnCaptured));

    (void)started;
  }

private:
  void OnCaptured(Dali::Ui::ImageView image)
  {
    mLastScreenshot = image;
  }

  Dali::Ui::ImageView mLastScreenshot;
};
```

For media state, enable platform video support with `Dali::Ui::WebView::SetVideoHoleEnabled` when needed, query it with `Dali::Ui::WebView::GetVideoHoleEnabled`, and use `Dali::Ui::WebView::CheckVideoPlayingAsynchronously` to receive the current playback state.

```cpp
class VideoStateController
{
public:
  void Check(WebView webView)
  {
    webView.SetVideoHoleEnabled(true);

    bool videoHoleEnabled = webView.GetVideoHoleEnabled();
    bool requestStarted = webView.CheckVideoPlayingAsynchronously(
      WebView::VideoPlayingCallback::New(this, &VideoStateController::OnVideoPlaying));

    (void)videoHoleEnabled;
    (void)requestStarted;
  }

private:
  void OnVideoPlaying(bool isPlaying)
  {
    mIsPlaying = isPlaying;
  }

  bool mIsPlaying{false};
};
```

`Dali::Ui::WebView::Suspend`, `Dali::Ui::WebView::Resume`, `Dali::Ui::WebView::SuspendNetworkLoading`, `Dali::Ui::WebView::ResumeNetworkLoading`, and `Dali::Ui::WebView::ClearAllTilesResources` are useful when app lifecycle or memory policy requires web content to pause or release resources.

```cpp
void ApplyWebLifecyclePolicy(WebView webView, bool active)
{
  if(active)
  {
    webView.Resume();
    webView.ResumeNetworkLoading();
  }
  else
  {
    webView.SuspendNetworkLoading();
    webView.Suspend();
    webView.ClearAllTilesResources();
  }
}
```

## Fullscreen, Geolocation, and Process Events

`Dali::Ui::WebView::FullscreenEnteredSignal` and `Dali::Ui::WebView::FullscreenExitedSignal` notify app code when web content changes fullscreen state. Call `Dali::Ui::WebView::ExitFullscreen` to leave fullscreen from application UI.

```cpp
class FullscreenController : public Dali::ConnectionTracker
{
public:
  void Attach(WebView webView)
  {
    mWebView = webView;

    mWebView.FullscreenEnteredSignal().Connect(
      this,
      &FullscreenController::OnFullscreenEntered);

    mWebView.FullscreenExitedSignal().Connect(
      this,
      &FullscreenController::OnFullscreenExited);
  }

  void CloseFullscreen()
  {
    mWebView.ExitFullscreen();
  }

private:
  void OnFullscreenEntered(WebView webView)
  {
    (void)webView;
    mFullscreen = true;
  }

  void OnFullscreenExited(WebView webView)
  {
    (void)webView;
    mFullscreen = false;
  }

  WebView mWebView;
  bool mFullscreen{false};
};
```

`Dali::Ui::WebView::GeolocationPermissionSignal` lets the application answer geolocation permission requests. The callback receives the requesting host and protocol and returns `true` or `false`.

```cpp
class PermissionController : public Dali::ConnectionTracker
{
public:
  void Attach(WebView webView)
  {
    webView.GeolocationPermissionSignal().Connect(
      this,
      &PermissionController::OnGeolocationPermission);
  }

private:
  bool OnGeolocationPermission(WebView webView, const Dali::String& host, const Dali::String& protocol)
  {
    (void)webView;
    (void)host;
    return protocol == Dali::String("https");
  }
};
```

Use `Dali::Ui::WebView::WebProcessCrashedSignal` to recover app UI when the web process exits unexpectedly.

```cpp
class ProcessController : public Dali::ConnectionTracker
{
public:
  void Attach(WebView webView)
  {
    webView.WebProcessCrashedSignal().Connect(
      this,
      &ProcessController::OnWebProcessCrashed);
  }

private:
  void OnWebProcessCrashed(WebView webView)
  {
    webView.LoadHtmlString(Dali::String(
      "<html><body><h1>Page unavailable</h1></body></html>"));
  }
};
```

## Supporting Web View Types

`Dali::Ui::WebViewOverScrolled` describes the direction passed by `Dali::Ui::WebView::OverScrolledSignal`. The available values include `Dali::Ui::WebViewOverScrolled::LEFT` and `Dali::Ui::WebViewOverScrolled::BOTTOM`.

```cpp
bool IsTerminalOverScroll(WebViewOverScrolled direction)
{
  return direction == WebViewOverScrolled::LEFT ||
         direction == WebViewOverScrolled::BOTTOM;
}
```

`Dali::Ui::WebViewFindOption` defines flags for web view text-find behavior. Its values include `Dali::Ui::WebViewFindOption::NONE`, `Dali::Ui::WebViewFindOption::CASE_INSENSITIVE`, `Dali::Ui::WebViewFindOption::AT_WORD_STARTS`, `Dali::Ui::WebViewFindOption::TREAT_MEDIAL_CAPITAL_AS_WORD_START`, `Dali::Ui::WebViewFindOption::BACKWARDS`, `Dali::Ui::WebViewFindOption::WRAP_AROUND`, `Dali::Ui::WebViewFindOption::SHOW_OVERLAY`, `Dali::Ui::WebViewFindOption::SHOW_FIND_INDICATOR`, and `Dali::Ui::WebViewFindOption::SHOW_HIGHLIGHT`.

```cpp
uint32_t MakeFindOptionMask()
{
  uint32_t options = static_cast<uint32_t>(WebViewFindOption::CASE_INSENSITIVE) |
                     static_cast<uint32_t>(WebViewFindOption::WRAP_AROUND) |
                     static_cast<uint32_t>(WebViewFindOption::SHOW_HIGHLIGHT);

  return options;
}
```
