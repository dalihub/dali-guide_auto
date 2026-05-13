# web-view Evaluation - Trial 1

## Summary

- Status: needs_review
- Publish ready: true
- Bugs: 0
- Warnings: 1
- Needs review: 9
- Repairable issues: 1

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 1 |
| UNKNOWN_SCOPED_SYMBOL | 9 |

## Human Review

### 1. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 228
- Span: `Dali::Ui::WebView::JavaScriptCallback::New`
- Reason: Dali::Ui::WebView::JavaScriptCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 248
- Span: `Dali::Ui::WebView::JavaScriptCallback::New`
- Reason: Dali::Ui::WebView::JavaScriptCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 272
- Span: `Dali::Ui::WebView::JavaScriptAlertCallback::New`
- Reason: Dali::Ui::WebView::JavaScriptAlertCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 4. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 275
- Span: `Dali::Ui::WebView::JavaScriptConfirmCallback::New`
- Reason: Dali::Ui::WebView::JavaScriptConfirmCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 5. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 278
- Span: `Dali::Ui::WebView::JavaScriptPromptCallback::New`
- Reason: Dali::Ui::WebView::JavaScriptPromptCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 6. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 364
- Span: `Dali::BoundsInteger`
- Reason: Dali::BoundsInteger could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 7. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 375
- Span: `Dali::BoundsInteger`
- Reason: Dali::BoundsInteger could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 8. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 380
- Span: `Dali::Ui::WebView::ScreenshotCapturedCallback::New`
- Reason: Dali::Ui::WebView::ScreenshotCapturedCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 9. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 404
- Span: `Dali::Ui::WebView::PlainTextCallback::New`
- Reason: Dali::Ui::WebView::PlainTextCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 10. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 50
- Span: `void(Dali::Ui::WebView, const Dali::String&`
- Reason: Backticked API-like token `void(Dali::Ui::WebView, const Dali::String&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/web-view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/web-view.md`
