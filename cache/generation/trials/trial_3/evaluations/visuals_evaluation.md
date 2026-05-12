# visuals Evaluation - Trial 3

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 2
- Warnings: 1
- Needs review: 1
- Repairable issues: 3

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 1 |
| UNKNOWN_RECEIVER_METHOD | 2 |
| UNKNOWN_SCOPED_SYMBOL | 1 |

## Human Review

### 1. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 154
- Span: `Dali::AnimatedImage::PlayState`
- Reason: Dali::AnimatedImage::PlayState could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 2. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 163
- Span: `playRange.PushBack(`
- Reason: playRange.PushBack() does not resolve on inferred type Dali::Property::Array.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 3. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 164
- Span: `playRange.PushBack(`
- Reason: playRange.PushBack() does not resolve on inferred type Dali::Property::Array.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 4. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 36
- Span: `Dali::Ui::AnimatedImageVisual&`
- Reason: Backticked API-like token `Dali::Ui::AnimatedImageVisual&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_3/validation/visuals.validation.json`
- Source draft: `cache/generation/trials/trial_3/drafts/visuals.md`
