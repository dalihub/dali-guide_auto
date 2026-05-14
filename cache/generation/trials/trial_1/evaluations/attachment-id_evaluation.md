# attachment-id Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 1
- Needs review: 2
- Repairable issues: 2

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 1 |
| UNKNOWN_RECEIVER_METHOD | 1 |
| UNKNOWN_SCOPED_SYMBOL | 2 |

## Human Review

### 1. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 56
- Span: `Dali::MakeUnique`
- Reason: Dali::MakeUnique could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 99
- Span: `Dali::Move`
- Reason: Dali::Move could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 136
- Span: `button.Initialize(`
- Reason: button.Initialize() does not resolve on inferred type MyButton.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 4. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 29
- Span: `Dali::UniquePtr<T>`
- Reason: Backticked API-like token `Dali::UniquePtr<T>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/attachment-id.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/attachment-id.md`
