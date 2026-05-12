# web-view Evaluation - Trial 1

## Summary

- Status: needs_review
- Publish ready: true
- Bugs: 0
- Warnings: 1
- Needs review: 8
- Repairable issues: 1

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 1 |
| UNKNOWN_SCOPED_SYMBOL | 8 |

## Human Review

### 1. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 253
- Span: `WebView::JavaScriptCallback::New`
- Reason: WebView::JavaScriptCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 281
- Span: `WebView::JavaScriptCallback::New`
- Reason: WebView::JavaScriptCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 305
- Span: `WebView::JavaScriptAlertCallback::New`
- Reason: WebView::JavaScriptAlertCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 4. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 308
- Span: `WebView::JavaScriptConfirmCallback::New`
- Reason: WebView::JavaScriptConfirmCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 5. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 311
- Span: `WebView::JavaScriptPromptCallback::New`
- Reason: WebView::JavaScriptPromptCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 6. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 450
- Span: `WebView::PlainTextCallback::New`
- Reason: WebView::PlainTextCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 7. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 479
- Span: `WebView::ScreenshotCapturedCallback::New`
- Reason: WebView::ScreenshotCapturedCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 8. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 506
- Span: `WebView::VideoPlayingCallback::New`
- Reason: WebView::VideoPlayingCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 9. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 38
- Span: `Dali::Ui::WebView&`
- Reason: Backticked API-like token `Dali::Ui::WebView&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/web-view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/web-view.md`
