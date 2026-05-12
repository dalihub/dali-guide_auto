# lottie-animation-view Evaluation - Trial 3

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 3
- Warnings: 3
- Needs review: 0
- Repairable issues: 6

## Issue Counts

| Type | Count |
|---|---:|
| PREFERRED_USAGE_PATTERN_MISSING | 1 |
| PROMPT_LEAKAGE | 1 |
| UNKNOWN_BACKTICK_API_TOKEN | 2 |
| UNKNOWN_RECEIVER_METHOD | 2 |

## Human Review

### 1. PROMPT_LEAKAGE (bug)

- Location: line 66
- Span: `Placeholder`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

### 2. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 300
- Span: `contentInfo.Count(`
- Reason: contentInfo.Count() does not resolve on inferred type Dali::Property::Map.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 3. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 301
- Span: `markerInfo.Count(`
- Reason: markerInfo.Count() does not resolve on inferred type Dali::Property::Map.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 4. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 132
- Span: `PropertyRange::PROPERTY_START_INDEX`
- Reason: Backticked API-like token `PropertyRange::PROPERTY_START_INDEX` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 5. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 132
- Span: `PropertyRange::PROPERTY_END_INDEX`
- Reason: Backticked API-like token `PropertyRange::PROPERTY_END_INDEX` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 6. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: lottie-animation-view_signals
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_3/validation/lottie-animation-view.validation.json`
- Source draft: `cache/generation/trials/trial_3/drafts/lottie-animation-view.md`
