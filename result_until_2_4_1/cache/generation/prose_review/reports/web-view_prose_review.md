# Web View Prose Review

Changed prose:
- Tightened the opening sentence from “embeds web content” to “displays web content” to match the public header wording for `Dali::Ui::WebView`.
- Clarified `LoadUrl` prose to say “remote URLs or file URLs,” matching the sample’s `file://` loading path.
- Clarified navigation signal prose so `UrlChangedSignal` is included with signals that pass both `WebView` and URL.
- Changed “object name” to “exposed object name” for `AddJavaScriptMessageHandler`, matching the public parameter name.
- Corrected scroll signal prose from “web-view scroll edge type” to the exact public type `Dali::Ui::WebViewScrollEdge`.
- Added a small `ScrollEdgeReachedSignal` handler to the existing interaction example because the prose already described that signal.
- Added `Dali::Ui::WebViewScrollEdge` to the supporting types section.
- Revised the `Dali::Ui::WebViewFindOption` paragraph to avoid implying a public text-search method exists on `Dali::Ui::WebView`; the current public header exposes the enum and `TextFoundSignal`, but no public search-start API.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/web-view.h`: `WebView` inherits `Dali::Ui::View`; `New`, typed setters/getters, navigation methods, JavaScript methods, screenshot APIs, lifecycle/network methods, and signals are public.
- `repos/dali-ui/dali-ui-foundation/public-api/web-view.h`: signal signatures confirm page-load and URL-change signals pass `(WebView, const Dali::String&)`, `FrameRenderedSignal` passes `WebView`, `ScrollEdgeReachedSignal` passes `WebViewScrollEdge`, `OverScrolledSignal` passes `WebViewOverScrolled`, geolocation returns `bool`, and crash/fullscreen signals pass `WebView`.
- `repos/dali-ui/dali-ui-foundation/public-api/web-view-types.h`: `WebViewScrollEdge`, `WebViewOverScrolled`, and `WebViewFindOption` enum values are public.
- `repos/dali-ui/samples/web-view/web-view-example.cpp`: sample creates `WebView::New`, adds it to the window, connects page-load/URL/frame signals, uses `LoadUrl`, `LoadHtmlString`, `EvaluateJavaScript`, navigation, and asynchronous screenshot capture.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-WebView.cpp`: UTCs cover the listed setters/getters, navigation, loading, JavaScript callbacks/replies, screenshot APIs, input, video state, signal signatures, and enum values.

Remaining concerns:
- The final `TextFoundSignal` example uses result handling only. The reviewed public `Dali::Ui::WebView` header does not expose a method that starts a text search with `Dali::Ui::WebViewFindOption`.
- The draft examples were source-checked for public API shape, but they were not compiled in this read-only review session.
