---
title: Web View
sidebar_label: Web View
category: views-components
---

# Web View

WebView is a View component for displaying web content within dali-ui applications. It enables embedding web pages and provides navigation, JavaScript interaction, and page load monitoring capabilities.

## Table of Contents

- [Creating a WebView](#creating-a-webview)
- [Loading Content](#loading-content)
- [Navigation and History](#navigation-and-history)
- [Page Load Monitoring](#page-load-monitoring)
- [JavaScript Interaction](#javascript-interaction)
- [Scroll Control](#scroll-control)
- [Zoom and Scale](#zoom-and-scale)
- [Screenshots and Page Information](#screenshots-and-page-information)
- [Input Event Handling](#input-event-handling)
- [Custom Headers and User Agent](#custom-headers-and-user-agent)
- [Resource Management](#resource-management)
- [Signals](#signals)

## Creating a WebView

Create a WebView using `WebView::New()`. The WebView inherits from View, so you can use standard View methods to size and position it.

```cpp
#include <dali-ui-foundation/public-api/web-view.h>

using namespace Dali;
using namespace Dali::Ui;

// Create an empty WebView
WebView webView = WebView::New();

// Create and load a URL immediately
WebView webView = WebView::New(Dali::String("https://www.example.com"));

// Size and position the WebView
webView.SetRequestedWidth(800.0f)
      .SetRequestedHeight(600.0f)
      .SetRequestedPositionX(0.0f)
      .SetRequestedPositionY(0.0f);

// Add to window
window.Add(webView);
```

Use `WebView::DownCast()` to convert a base handle to a WebView:

```cpp
View baseView = webView;
WebView downcast = WebView::DownCast(baseView);
if (downcast)
{
  // Successfully downcast
}
```

## Loading Content

WebView provides multiple methods for loading web content.

### Loading a URL

Use `LoadUrl()` to navigate to a web address:

```cpp
webView.LoadUrl(Dali::String("https://www.example.com"));
```

### Loading HTML String

Use `LoadHtmlString()` to display inline HTML content:

```cpp
webView.LoadHtmlString(Dali::String(
  "<html><body>"
  "<h1>Hello from dali-ui</h1>"
  "</body></html>"));
```

To load HTML while replacing the current history entry, use `LoadHtmlStringOverrideCurrentEntry()`:

```cpp
webView.LoadHtmlStringOverrideCurrentEntry(
  Dali::String("<html><body>Content</body></html>"),
  Dali::String(""),  // base URI
  Dali::String("")); // unreachable URL
```

### Loading Raw Content

Use `LoadContents()` to load raw content with specified MIME type and encoding:

```cpp
const char* htmlContent = "<html><body>Test</body></html>";
webView.LoadContents(
  reinterpret_cast<const int8_t*>(htmlContent),
  strlen(htmlContent),
  Dali::String("text/html"),
  Dali::String("UTF-8"),
  Dali::String("")); // base URI
```

### Reloading and Stopping

```cpp
// Reload the current page
webView.Reload();

// Reload without using cache
webView.ReloadWithoutCache();

// Stop loading
webView.StopLoading();
```

## Navigation and History

WebView maintains a navigation history for back and forward navigation.

### Checking Navigation State

```cpp
if (webView.CanGoBack())
{
  webView.GoBack();
}

if (webView.CanGoForward())
{
  webView.GoForward();
}
```

### Clearing History

```cpp
webView.ClearHistory();
```

## Page Load Monitoring

WebView emits signals at different stages of page loading. Connect handlers to monitor load progress.

```cpp
class MyController : public ConnectionTracker
{
public:
  void SetupWebView()
  {
    mWebView = WebView::New();
    
    mWebView.PageLoadStartedSignal().Connect(this, &MyController::OnPageLoadStarted);
    mWebView.PageLoadInProgressSignal().Connect(this, &MyController::OnPageLoadInProgress);
    mWebView.PageLoadFinishedSignal().Connect(this, &MyController::OnPageLoadFinished);
    mWebView.UrlChangedSignal().Connect(this, &MyController::OnUrlChanged);
  }

  void OnPageLoadStarted(WebView view, const Dali::String& url)
  {
    // Page load has started
  }

  void OnPageLoadInProgress(WebView view, const Dali::String& url)
  {
    float progress = mWebView.GetLoadProgressPercentage();
    // progress is 0.0 to 100.0
  }

  void OnPageLoadFinished(WebView view, const Dali::String& url)
  {
    Dali::String title = mWebView.GetTitle();
    // Page is fully loaded
  }

  void OnUrlChanged(WebView view, const Dali::String& url)
  {
    // URL has changed
  }

private:
  WebView mWebView;
};
```

## JavaScript Interaction

WebView supports evaluating JavaScript and handling JavaScript dialogs.

### Evaluating JavaScript

Execute JavaScript code without a callback:

```cpp
webView.EvaluateJavaScript(Dali::String("document.title"));
```

Execute JavaScript and receive the result asynchronously:

```cpp
void OnJavaScriptResult(const Dali::String& result)
{
  // Handle the result
}

// In your setup code:
auto callback = WebView::JavaScriptCallback::New(this, &MyController::OnJavaScriptResult);
webView.EvaluateJavaScript(Dali::String("document.title"), std::move(callback));
```

### JavaScript Message Handler

Register a handler to receive messages from JavaScript:

```cpp
void OnJavaScriptMessage(const Dali::String& message)
{
  // Handle message from web page
}

// In your setup code:
auto callback = WebView::JavaScriptCallback::New(this, &MyController::OnJavaScriptMessage);
webView.AddJavaScriptMessageHandler(Dali::String("myNativeObject"), std::move(callback));
```

### JavaScript Dialog Callbacks

Handle JavaScript `alert()`, `confirm()`, and `prompt()` dialogs:

```cpp
bool OnJavaScriptAlert(const Dali::String& message)
{
  // Show alert to user
  webView.JavaScriptAlertReply();
  return true;
}

bool OnJavaScriptConfirm(const Dali::String& message)
{
  // Show confirm dialog
  bool userConfirmed = true; // Get user input
  webView.JavaScriptConfirmReply(userConfirmed);
  return true;
}

bool OnJavaScriptPrompt(const Dali::String& message, const Dali::String& defaultValue)
{
  // Show prompt dialog
  webView.JavaScriptPromptReply(Dali::String("user input"));
  return true;
}

// Register callbacks
webView.RegisterJavaScriptAlertCallback(
  WebView::JavaScriptAlertCallback::New(this, &MyController::OnJavaScriptAlert));
webView.RegisterJavaScriptConfirmCallback(
  WebView::JavaScriptConfirmCallback::New(this, &MyController::OnJavaScriptConfirm));
webView.RegisterJavaScriptPromptCallback(
  WebView::JavaScriptPromptCallback::New(this, &MyController::OnJavaScriptPrompt));
```

## Scroll Control

Query and control the scroll position of the web page.

### Querying Scroll State

```cpp
Vector2 position = webView.GetScrollPosition();
Vector2 scrollSize = webView.GetScrollSize();
Vector2 contentSize = webView.GetContentSize();
```

### Programmatic Scrolling

```cpp
webView.ScrollBy(10, 20);  // Scroll by delta pixels
webView.ScrollEdgeBy(10, 20); // Scroll edge by delta
```

### Scroll Signals

```cpp
mWebView.ScrollEdgeReachedSignal().Connect(this, &MyController::OnScrollEdgeReached);
mWebView.OverScrolledSignal().Connect(this, &MyController::OnOverScrolled);

void OnScrollEdgeReached(WebView view, WebViewScrollEdge edge)
{
  // edge is LEFT, RIGHT, TOP, or BOTTOM
}

void OnOverScrolled(WebView view, WebViewOverScrolled direction)
{
  // direction is LEFT, RIGHT, TOP, or BOTTOM
}
```

## Zoom and Scale

Control the zoom level of web content.

### Page Zoom

```cpp
webView.SetPageZoomFactor(1.5f);
float zoom = webView.GetPageZoomFactor();
```

### Text Zoom

```cpp
webView.SetTextZoomFactor(1.2f);
float textZoom = webView.GetTextZoomFactor();
```

### Scale Factor

```cpp
webView.SetScaleFactor(2.0f, Vector2(100.0f, 100.0f)); // Scale around a point
float scale = webView.GetScaleFactor();
```

## Screenshots and Page Information

### Taking Screenshots

Synchronous screenshot:

```cpp
Dali::BoundsInteger viewArea(0, 0, 100, 100);
Dali::Ui::ImageView screenshot = webView.GetScreenshot(viewArea, 1.0f);
```

Asynchronous screenshot:

```cpp
void OnScreenshotCaptured(Dali::Ui::ImageView screenshot)
{
  if (screenshot)
  {
    // Use the screenshot
  }
}

// In your setup code:
Dali::BoundsInteger viewArea(0, 0, 100, 100);
auto callback = WebView::ScreenshotCapturedCallback::New(this, &MyController::OnScreenshotCaptured);
webView.GetScreenshotAsynchronously(viewArea, 1.0f, std::move(callback));
```

### Page Information

```cpp
Dali::String url = webView.GetUrl();
Dali::String title = webView.GetTitle();
Dali::String selectedText = webView.GetSelectedText();
Dali::Ui::ImageView favicon = webView.GetFavicon();
```

### Plain Text Extraction

```cpp
void OnPlainTextReceived(const Dali::String& text)
{
  // Process the plain text
}

webView.GetPlainTextAsynchronously(
  WebView::PlainTextCallback::New(this, &MyController::OnPlainTextReceived));
```

## Input Event Handling

WebView can automatically handle input events or you can feed events manually.

### Automatic Event Handling

By default, WebView handles mouse and key events automatically. Control this behavior:

```cpp
webView.SetMouseEventsEnabled(true);
webView.SetKeyEventsEnabled(true);

bool mouseEnabled = webView.GetMouseEventsEnabled();
bool keyEnabled = webView.GetKeyEventsEnabled();
```

### Manual Event Feeding

When automatic handling is disabled, feed events manually:

```cpp
webView.SetMouseEventsEnabled(false);
webView.SetKeyEventsEnabled(false);

// Feed touch event
webView.FeedTouchEvent(touchEvent);

// Feed key event
bool consumed = webView.FeedKeyEvent(keyEvent);

// Feed mouse wheel
webView.FeedMouseWheel(true, 1, 100, 100); // yDirection, step, x, y
```

### Video Hole

Enable video hole for hardware-accelerated video playback:

```cpp
webView.SetVideoHoleEnabled(true);
bool enabled = webView.GetVideoHoleEnabled();
```

### Video Playing Check

```cpp
void OnVideoPlayingChecked(bool isPlaying)
{
  if (isPlaying)
  {
    // Video is currently playing
  }
}

webView.CheckVideoPlayingAsynchronously(
  WebView::VideoPlayingCallback::New(this, &MyController::OnVideoPlayingChecked));
```

## Custom Headers and User Agent

### Custom Headers

Add custom HTTP headers to all requests:

```cpp
webView.AddCustomHeader(Dali::String("X-Custom-Header"), Dali::String("CustomValue"));
webView.RemoveCustomHeader(Dali::String("X-Custom-Header"));
```

### User Agent

Set a custom user agent string:

```cpp
webView.SetUserAgent(Dali::String("MyApp/1.0"));
Dali::String agent = webView.GetUserAgent();
```

## Resource Management

### Suspend and Resume

```cpp
webView.Suspend();  // Suspend web view processing
webView.Resume();   // Resume web view processing
```

### Network Loading Control

```cpp
webView.SuspendNetworkLoading();
webView.ResumeNetworkLoading();
```

### Tile Management

```cpp
webView.SetTilesClearedWhenHidden(true);
webView.SetTileCoverAreaMultiplier(2.0f);
webView.ClearAllTilesResources();
```

### Document Appearance

```cpp
webView.SetDocumentBackgroundColor(Vector4(1.0f, 1.0f, 1.0f, 1.0f));
webView.SetCursorEnabledByClient(true);
```

### Fullscreen

```cpp
webView.ExitFullscreen();
```

## Signals

WebView provides signals for various events:

| Signal | Description |
|--------|-------------|
| `PageLoadStartedSignal()` | Emitted when page load starts |
| `PageLoadInProgressSignal()` | Emitted during page load progress |
| `PageLoadFinishedSignal()` | Emitted when page load completes |
| `UrlChangedSignal()` | Emitted when URL changes |
| `FrameRenderedSignal()` | Emitted when a frame is rendered |
| `ScrollEdgeReachedSignal()` | Emitted when scroll reaches an edge |
| `OverScrolledSignal()` | Emitted on over-scroll |
| `FullscreenEnteredSignal()` | Emitted when entering fullscreen |
| `FullscreenExitedSignal()` | Emitted when exiting fullscreen |
| `TextFoundSignal()` | Emitted when text search finds matches |
| `GeolocationPermissionSignal()` | Emitted for geolocation permission requests |
| `WebProcessCrashedSignal()` | Emitted when web process crashes |

### Signal Connection Examples

```cpp
// Frame rendered
mWebView.FrameRenderedSignal().Connect(this, &MyController::OnFrameRendered);

void OnFrameRendered(WebView view)
{
  // New frame has been rendered
}

// Fullscreen changes
mWebView.FullscreenEnteredSignal().Connect(this, &MyController::OnFullscreenEntered);
mWebView.FullscreenExitedSignal().Connect(this, &MyController::OnFullscreenExited);

void OnFullscreenEntered(WebView view) { }
void OnFullscreenExited(WebView view) { }

// Geolocation permission
mWebView.GeolocationPermissionSignal().Connect(this, &MyController::OnGeolocationPermission);

bool OnGeolocationPermission(WebView view, const Dali::String& host, const Dali::String& protocol)
{
  return true; // Grant permission
}

// Web process crash
mWebView.WebProcessCrashedSignal().Connect(this, &MyController::OnWebProcessCrashed);

void OnWebProcessCrashed(WebView view)
{
  // Handle crash - may want to reload
  view.Reload();
}

// Text found
mWebView.TextFoundSignal().Connect(this, &MyController::OnTextFound);

void OnTextFound(WebView view, uint32_t count)
{
  // count is the number of matches found
}
```

## WebViewFindOption

The `WebViewFindOption` enum provides options for text search operations:

| Option | Description |
|--------|-------------|
| `WebViewFindOption::NONE` | No options |
| `WebViewFindOption::CASE_INSENSITIVE` | Case-insensitive search |
| `WebViewFindOption::AT_WORD_STARTS` | Match at word starts |
| `WebViewFindOption::TREAT_MEDIAL_CAPITAL_AS_WORD_START` | Treat medial capitals as word starts |
| `WebViewFindOption::BACKWARDS` | Search backwards |
| `WebViewFindOption::WRAP_AROUND` | Wrap around at document end |
| `WebViewFindOption::SHOW_OVERLAY` | Show search overlay |
| `WebViewFindOption::SHOW_FIND_INDICATOR` | Show find indicator |
| `WebViewFindOption::SHOW_HIGHLIGHT` | Highlight matches |

Options can be combined using bitwise OR:

```cpp
uint32_t options = static_cast<uint32_t>(WebViewFindOption::CASE_INSENSITIVE) |
                   static_cast<uint32_t>(WebViewFindOption::WRAP_AROUND);
