# Web View Prose Review

Changed prose:
- Revised the opening sentence to state that `Dali::Ui::WebView` is a `Dali::Ui::View` for displaying web content, and added the platform web engine plugin requirement.
- Added that `Dali::Ui::WebView::New` has a URL overload that creates a web view and immediately loads the URL.
- Clarified that both page load and URL signals use `void(Dali::Ui::WebView, const Dali::String&)`.
- Changed the geolocation sentence from "returns `bool` from the handler" to "expects a handler that returns `bool`".
- Added a small `Dali::Ui::WebViewScrollEdge` paragraph and example for `Dali::Ui::WebView::ScrollEdgeReachedSignal`, matching the existing event-types section without restructuring it.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/web-view.h` documents `WebView` as a `View`, declares `WebView::New()`, `WebView::New(const Dali::String& url)`, typed setters, navigation methods, JavaScript callbacks, screenshot APIs, and all listed signals.
- `repos/dali-ui/dali-ui-foundation/public-api/web-view-types.h` defines `Dali::Ui::WebViewScrollEdge`, `Dali::Ui::WebViewOverScrolled`, and `Dali::Ui::WebViewFindOption`.
- `repos/dali-ui/samples/web-view/web-view-example.cpp` shows `WebView::New`, view sizing, `LoadUrl`, `LoadHtmlString`, `EvaluateJavaScript` with `WebView::JavaScriptCallback::New`, signal connections, and asynchronous screenshot capture.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-WebView.cpp` confirms signatures and basic usage for navigation, content loading, JavaScript callbacks, screenshots, input toggles, scroll signals, geolocation permission, process crash, and enum values.

Remaining concerns:
- `Dali::Ui::WebViewFindOption` is public, but this public header does not expose a corresponding find-text method in the reviewed surface. The guide keeps the enum description scoped to the option type and `TextFoundSignal` rather than inventing a search API.
- The examples remain API-shape examples. They were checked against public declarations and nearby sample/UTC usage, but were not compiled as standalone snippets in this read-only review pass.
