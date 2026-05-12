# render-effects Evaluation - Trial 1

## Summary

- Status: needs_review
- Publish ready: true
- Bugs: 0
- Warnings: 0
- Needs review: 3
- Repairable issues: 0

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_SCOPED_SYMBOL | 3 |

## Human Review

### 1. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 248
- Span: `Dali::AlphaFunction::EASE_IN_OUT`
- Reason: Dali::AlphaFunction::EASE_IN_OUT could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 268
- Span: `Dali::AlphaFunction::EASE_OUT`
- Reason: Dali::AlphaFunction::EASE_OUT could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 284
- Span: `Dali::AlphaFunction::EASE_IN`
- Reason: Dali::AlphaFunction::EASE_IN could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/render-effects.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/render-effects.md`
