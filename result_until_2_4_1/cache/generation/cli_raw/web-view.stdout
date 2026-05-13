---
title: Web View
sidebar_label: Web View
category: views-components
---

# Web View

`Dali::Ui::WebView` embeds web content in a dali-ui application while staying in the `Dali::Ui::View` object model.

## Table of Contents

- [Create and Configure a Web View](#create-and-configure-a-web-view)
- [Load Pages and Local Content](#load-pages-and-local-content)
- [Navigate and Track Page State](#navigate-and-track-page-state)
- [Handle JavaScript](#handle-javascript)
- [Capture Page Content](#capture-page-content)
- [Control Input, Scrolling, and Fullscreen](#control-input-scrolling-and-fullscreen)
- [Manage Rendering, Network, and Media State](#manage-rendering-network-and-media-state)
- [Web View Supporting Types](#web-view-supporting-types)

## Create and Configure a Web View

Create a web view with `Dali::Ui::WebView::New`. `Dali::Ui::WebView` is a `Dali::Ui::View`, so application code can keep it in the dali-ui view tree and configure it through typed web-view setters instead of raw property calls.

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

Use the matching getters when UI state depends on the current configuration.

```cpp
const bool mouseEnabled = webView.GetMouseEventsEnabled();
const bool keyEnabled   = webView.GetKeyEventsEnabled();
const bool videoHole    = webView.GetVideoHoleEnabled();

const float pageZoom = webView.GetPageZoomFactor();
const float textZoom = webView.GetTextZoomFactor();
const float scale    = webView.GetScaleFactor();

Dali::String userAgent = webView.GetUserAgent();
```

When a handle is stored as a base `Dali::Ui::View`, recover the web-view API with `Dali::Ui::WebView::DownCast`.

```cpp
Dali::Ui::View view = webView;

Dali::Ui::WebView castWebView = Dali::Ui::WebView::DownCast(view);
if(castWebView)
{
  castWebView.Reload();
}
```

## Load Pages and Local Content

Use `Dali::Ui::WebView::LoadUrl` for remote or file URLs. Use `Dali::Ui::WebView::LoadHtmlString` when the application already has an HTML string.

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

For byte buffers, `Dali::Ui::WebView::LoadContents` accepts the content pointer, byte size, MIME type, encoding, and base URI. `Dali::Ui::WebView::LoadHtmlStringOverrideCurrentEntry` loads an HTML document while replacing the current history entry.

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

Use custom headers before loading the request that needs them.

```cpp
if(webView.AddCustomHeader(Dali::String("X-App-Mode"), Dali::String("embedded")))
{
  webView.LoadUrl(Dali::String("https://www.example.com/dashboard"));
  webView.RemoveCustomHeader(Dali::String("X-App-Mode"));
}
```

## Navigate and Track Page State

`Dali::Ui::WebView` owns page navigation commands such as `Dali::Ui::WebView::GoBack`, `Dali::Ui::WebView::GoForward`, `Dali::Ui::WebView::Reload`, `Dali::Ui::WebView::ReloadWithoutCache`, `Dali::Ui::WebView::StopLoading`, and `Dali::Ui::WebView::ClearHistory`.

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

Connect to web-view signals to keep application UI synchronized with the page. Page-load signals pass the `Dali::Ui::WebView` and the URL. `Dali::Ui::WebView::FrameRenderedSignal` passes the `Dali::Ui::WebView`.

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

## Handle JavaScript

Use `Dali::Ui::WebView::EvaluateJavaScript` for application-driven script execution. The callback overload returns the script result as a `Dali::String`.

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

`Dali::Ui::WebView::AddJavaScriptMessageHandler` registers an object name that page scripts can use to send messages to the application-side callback.

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

JavaScript dialogs are handled by registering callbacks and later replying through the matching reply method. `Dali::Ui::WebView::RegisterJavaScriptAlertCallback` pairs with `Dali::Ui::WebView::JavaScriptAlertReply`; `Dali::Ui::WebView::RegisterJavaScriptConfirmCallback` pairs with `Dali::Ui::WebView::JavaScriptConfirmReply`; `Dali::Ui::WebView::RegisterJavaScriptPromptCallback` pairs with `Dali::Ui::WebView::JavaScriptPromptReply`.

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

## Capture Page Content

`Dali::Ui::WebView::GetScreenshot` returns a `Dali::Ui::ImageView` for the requested rectangle and scale. Use `Dali::Ui::WebView::GetScreenshotAsynchronously` when capture work should complete through a callback.

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

Use the page information APIs to update surrounding app chrome without inspecting the raw page yourself.

```cpp
Dali::String title        = webView.GetTitle();
Dali::String url          = webView.GetUrl();
Dali::String selectedText = webView.GetSelectedText();

Dali::Vector2 scrollPosition = webView.GetScrollPosition();
Dali::Vector2 scrollSize     = webView.GetScrollSize();
Dali::Vector2 contentSize    = webView.GetContentSize();

Dali::Ui::ImageView favicon = webView.GetFavicon();
```

Plain text extraction is asynchronous.

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

## Control Input, Scrolling, and Fullscreen

`Dali::Ui::WebView` exposes input enablement through typed setters and getters.

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

Use `Dali::Ui::WebView::ScrollBy` for ordinary scroll deltas and `Dali::Ui::WebView::ScrollEdgeBy` when edge-aware scrolling is needed.

```cpp
webView.ScrollBy(0, 120);

const bool edgeScrolled = webView.ScrollEdgeBy(0, 80);
if(!edgeScrolled)
{
  Dali::Vector2 position = webView.GetScrollPosition();
}
```

Scroll and fullscreen events are delivered through web-view signals. `Dali::Ui::WebView::OverScrolledSignal` uses `Dali::Ui::WebViewOverScrolled`, and `Dali::Ui::WebView::ScrollEdgeReachedSignal` uses the web-view scroll edge type.

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

Exit fullscreen from the application with `Dali::Ui::WebView::ExitFullscreen`.

```cpp
webView.ExitFullscreen();
```

## Manage Rendering, Network, and Media State

Use `Dali::Ui::WebView::Suspend` and `Dali::Ui::WebView::Resume` for the web view lifecycle, and `Dali::Ui::WebView::SuspendNetworkLoading` or `Dali::Ui::WebView::ResumeNetworkLoading` when only network activity should be paused or resumed.

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

Tile and scale controls are owned by `Dali::Ui::WebView`. They are useful when the surrounding app changes page presentation or visibility.

```cpp
webView
  .SetTilesClearedWhenHidden(true)
  .SetTileCoverAreaMultiplier(2.0f);

webView.SetScaleFactor(1.25f, Dali::Vector2(120.0f, 160.0f));

const float scale = webView.GetScaleFactor();

webView.ClearAllTilesResources();
```

Video state is checked asynchronously with `Dali::Ui::WebView::CheckVideoPlayingAsynchronously`.

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

Handle geolocation permission requests and web process crashes through the owning signals.

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

## Web View Supporting Types

`Dali::Ui::WebViewOverScrolled` describes the over-scroll direction reported by `Dali::Ui::WebView::OverScrolledSignal`. Use values such as `Dali::Ui::WebViewOverScrolled::LEFT` and `Dali::Ui::WebViewOverScrolled::BOTTOM` in the signal handler.

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

`Dali::Ui::WebViewFindOption` is part of the web-view text-search surface. It defines combinable flags such as `Dali::Ui::WebViewFindOption::NONE`, `Dali::Ui::WebViewFindOption::CASE_INSENSITIVE`, `Dali::Ui::WebViewFindOption::AT_WORD_STARTS`, `Dali::Ui::WebViewFindOption::TREAT_MEDIAL_CAPITAL_AS_WORD_START`, `Dali::Ui::WebViewFindOption::BACKWARDS`, `Dali::Ui::WebViewFindOption::WRAP_AROUND`, `Dali::Ui::WebViewFindOption::SHOW_OVERLAY`, `Dali::Ui::WebViewFindOption::SHOW_FIND_INDICATOR`, and `Dali::Ui::WebViewFindOption::SHOW_HIGHLIGHT`.

```cpp
uint32_t findOptions =
  static_cast<uint32_t>(Dali::Ui::WebViewFindOption::CASE_INSENSITIVE) |
  static_cast<uint32_t>(Dali::Ui::WebViewFindOption::WRAP_AROUND) |
  static_cast<uint32_t>(Dali::Ui::WebViewFindOption::SHOW_HIGHLIGHT);

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
