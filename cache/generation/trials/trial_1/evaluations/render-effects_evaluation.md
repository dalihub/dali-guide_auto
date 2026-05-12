# render-effects Evaluation - Trial 1

## Summary

- Status: needs_review
- Publish ready: true
- Bugs: 0
- Warnings: 4
- Needs review: 6
- Repairable issues: 4

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 4 |
| UNKNOWN_SCOPED_SYMBOL | 6 |

## Human Review

### 1. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 138
- Span: `Dali::Ui::MaskEffect::ALPHA`
- Reason: Dali::Ui::MaskEffect::ALPHA could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 156
- Span: `Dali::Ui::MaskEffect::LUMINANCE`
- Reason: Dali::Ui::MaskEffect::LUMINANCE could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 213
- Span: `Dali::AlphaFunction::EASE_IN_OUT`
- Reason: Dali::AlphaFunction::EASE_IN_OUT could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 4. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 220
- Span: `Dali::AlphaFunction::EASE_IN_OUT`
- Reason: Dali::AlphaFunction::EASE_IN_OUT could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 5. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 236
- Span: `Dali::AlphaFunction::EASE_IN_OUT`
- Reason: Dali::AlphaFunction::EASE_IN_OUT could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 6. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 243
- Span: `Dali::AlphaFunction::EASE_IN_OUT`
- Reason: Dali::AlphaFunction::EASE_IN_OUT could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 7. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 35
- Span: `Dali::Ui::GaussianBlurEffect::New(uint32_t blurRadius`
- Reason: Backticked API-like token `Dali::Ui::GaussianBlurEffect::New(uint32_t blurRadius` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 8. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 63
- Span: `Dali::Ui::MaskEffect::LUMINANCE`
- Reason: Backticked API-like token `Dali::Ui::MaskEffect::LUMINANCE` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 9. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 95
- Span: `Dali::Ui::GaussianBlurEffect::SetBlurOnce(true`
- Reason: Backticked API-like token `Dali::Ui::GaussianBlurEffect::SetBlurOnce(true` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 10. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 95
- Span: `Dali::Ui::BackgroundBlurEffect::SetBlurOnce(true`
- Reason: Backticked API-like token `Dali::Ui::BackgroundBlurEffect::SetBlurOnce(true` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/render-effects.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/render-effects.md`
