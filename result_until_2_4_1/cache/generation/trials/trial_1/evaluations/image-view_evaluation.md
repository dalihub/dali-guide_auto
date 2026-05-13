# image-view Evaluation - Trial 1

## Summary

- Status: warning
- Publish ready: true
- Bugs: 0
- Warnings: 4
- Needs review: 0
- Repairable issues: 4

## Issue Counts

| Type | Count |
|---|---:|
| PREFERRED_USAGE_PATTERN_MISSING | 1 |
| UNKNOWN_BACKTICK_API_TOKEN | 3 |

## Human Review

### 1. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 24
- Span: `Dali::Ui::ImageView::New(const Dali::String&`
- Reason: Backticked API-like token `Dali::Ui::ImageView::New(const Dali::String&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 76
- Span: `LoadPolicy::ATTACHED`
- Reason: Backticked API-like token `LoadPolicy::ATTACHED` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 3. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 76
- Span: `ReleasePolicy::DETACHED`
- Reason: Backticked API-like token `ReleasePolicy::DETACHED` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 4. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: view_animation
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/image-view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/image-view.md`
