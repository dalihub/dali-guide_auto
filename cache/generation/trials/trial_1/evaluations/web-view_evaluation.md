# web-view Evaluation - Trial 1

## Summary

- Status: needs_review
- Publish ready: true
- Bugs: 0
- Warnings: 9
- Needs review: 10
- Repairable issues: 9

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 3 |
| THIN_SECTION | 6 |
| UNKNOWN_SCOPED_SYMBOL | 10 |

## Human Review

### 1. THIN_SECTION (warning)

- Location: line 63
- Span: `## Loading Content`
- Reason: Section ## Loading Content has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 2. THIN_SECTION (warning)

- Location: line 122
- Span: `## Navigation and History`
- Reason: Section ## Navigation and History has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 3. THIN_SECTION (warning)

- Location: line 191
- Span: `## JavaScript Interaction`
- Reason: Section ## JavaScript Interaction has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 4. THIN_SECTION (warning)

- Location: line 267
- Span: `## Scroll Control`
- Reason: Section ## Scroll Control has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 5. THIN_SECTION (warning)

- Location: line 303
- Span: `## Zoom and Scale`
- Reason: Section ## Zoom and Scale has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 6. EMPTY_SECTION (warning)

- Location: line 328
- Span: ``
- Reason: Section ## Screenshots and Page Information is empty.

### 7. THIN_SECTION (warning)

- Location: line 377
- Span: `## Input Event Handling`
- Reason: Section ## Input Event Handling has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 8. EMPTY_SECTION (warning)

- Location: line 435
- Span: ``
- Reason: Section ## Custom Headers and User Agent is empty.

### 9. EMPTY_SECTION (warning)

- Location: line 455
- Span: ``
- Reason: Section ## Resource Management is empty.

### 10. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 211
- Span: `WebView::JavaScriptCallback::New`
- Reason: WebView::JavaScriptCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 11. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 226
- Span: `WebView::JavaScriptCallback::New`
- Reason: WebView::JavaScriptCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 12. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 259
- Span: `WebView::JavaScriptAlertCallback::New`
- Reason: WebView::JavaScriptAlertCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 13. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 261
- Span: `WebView::JavaScriptConfirmCallback::New`
- Reason: WebView::JavaScriptConfirmCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 14. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 263
- Span: `WebView::JavaScriptPromptCallback::New`
- Reason: WebView::JavaScriptPromptCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 15. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 334
- Span: `Dali::BoundsInteger`
- Reason: Dali::BoundsInteger could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 16. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 350
- Span: `Dali::BoundsInteger`
- Reason: Dali::BoundsInteger could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 17. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 351
- Span: `WebView::ScreenshotCapturedCallback::New`
- Reason: WebView::ScreenshotCapturedCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 18. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 373
- Span: `WebView::PlainTextCallback::New`
- Reason: WebView::PlainTextCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 19. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 431
- Span: `WebView::VideoPlayingCallback::New`
- Reason: WebView::VideoPlayingCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/web-view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/web-view.md`
