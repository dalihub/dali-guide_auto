# attachment-id Evaluation - Trial 1

## Summary

- Status: needs_review
- Publish ready: true
- Bugs: 0
- Warnings: 3
- Needs review: 3
- Repairable issues: 3

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 3 |
| UNKNOWN_SCOPED_SYMBOL | 3 |

## Human Review

### 1. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 66
- Span: `Dali::MakeUnique`
- Reason: Dali::MakeUnique could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 104
- Span: `Dali::Move`
- Reason: Dali::Move could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 153
- Span: `Dali::MakeUnique`
- Reason: Dali::MakeUnique could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 4. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 35
- Span: `Dali::UniquePtr<T>`
- Reason: Backticked API-like token `Dali::UniquePtr<T>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 5. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 43
- Span: `View::GetAttachment<T>`
- Reason: Backticked API-like token `View::GetAttachment<T>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 6. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 51
- Span: `View::DetachAttachment<T>`
- Reason: Backticked API-like token `View::DetachAttachment<T>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/attachment-id.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/attachment-id.md`
