# image-loader Evaluation - Trial 1

## Summary

- Status: needs_review
- Publish ready: true
- Bugs: 0
- Warnings: 1
- Needs review: 5
- Repairable issues: 1

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 1 |
| UNKNOWN_SCOPED_SYMBOL | 5 |

## Human Review

### 1. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 37
- Span: `Dali::ImageDimensions`
- Reason: Dali::ImageDimensions could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 65
- Span: `Dali::ImageDimensions`
- Reason: Dali::ImageDimensions could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 88
- Span: `Dali::ImageDimensions`
- Reason: Dali::ImageDimensions could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 4. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 89
- Span: `Dali::ImageDimensions`
- Reason: Dali::ImageDimensions could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 5. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 145
- Span: `Dali::ImageDimensions`
- Reason: Dali::ImageDimensions could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 6. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 27
- Span: `Dali::ImageDimensions`
- Reason: Backticked API-like token `Dali::ImageDimensions` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/image-loader.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/image-loader.md`
