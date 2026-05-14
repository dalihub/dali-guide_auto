# Web View Prose Review

Changed prose:
- Clarified `LoadHtmlStringOverrideCurrentEntry` wording to say it loads supplied HTML while overriding the current history entry, matching the public method brief and parameters.
- Changed “The fluent setters return `Dali::Ui::WebView&`” to “Most configuration setters return `Dali::Ui::WebView&`” because `SetScaleFactor` is a configuration method but returns `void`.
- Split input-forwarding guidance so `FeedKeyEvent` and `FeedTouchEvent` are tied to disabled automatic forwarding, while `FeedMouseWheel` is described only as direct wheel input.
- Reworded JavaScript message handler prose to mention the exposed object name and string message callback.
- Reworded `Dali::Ui::WebViewFindOption` as bit flags for find-text options, matching `web-view-types.h`.
- Added `Dali::Ui::WebViewScrollEdge` to the scroll-signal prose, because `ScrollEdgeReachedSignal` uses that type and `OverScrolledSignal` uses `Dali::Ui::WebViewOverScrolled`.
- Clarified runtime resource prose so `ClearHistory`, `ClearAllTilesResources`, and tile-related setters are not presented as the same kind of clearing operation.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/web-view.h` defines `Dali::Ui::WebView` as a `Dali::Ui::View`, documents the web engine plugin requirement, and provides the listed public methods and signal signatures.
- `repos/dali-ui/dali-ui-foundation/public-api/web-view.h` shows chainable setters returning `WebView&`, while `SetScaleFactor(float, Dali::Vector2)` returns `void`.
- `repos/dali-ui/dali-ui-foundation/public-api/web-view.h` documents automatic forwarding only in the `FeedKeyEvent` and `FeedTouchEvent` comments.
- `repos/dali-ui/dali-ui-foundation/public-api/web-view-types.h` defines `WebViewScrollEdge`, `WebViewOverScrolled`, and `WebViewFindOption : uint32_t` with combinable bit values.
- `repos/dali-ui/samples/web-view/web-view-example.cpp` and `repos/dali-ui/samples/web-view/web-browser-example.cpp` show app-facing usage of `WebView::New`, `LoadUrl`, navigation methods, page-load signals, `UrlChangedSignal`, and `FrameRenderedSignal`.

Remaining concerns:
- The code examples were preserved as requested and checked against public signatures, but they were not compiled in this review pass.
- `Dali::Ui::WebViewFindOption` is public and accurate, but this header snapshot does not expose a find-text method that consumes these flags.
