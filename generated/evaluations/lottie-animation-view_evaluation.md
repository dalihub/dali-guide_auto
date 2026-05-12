# lottie-animation-view Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 1
- Needs review: 9
- Repairable issues: 2

## Issue Counts

| Type | Count |
|---|---:|
| PREFERRED_USAGE_PATTERN_MISSING | 1 |
| PROMPT_LEAKAGE | 1 |
| UNKNOWN_SCOPED_SYMBOL | 9 |

## Human Review

### 1. PROMPT_LEAKAGE (bug)

- Location: line 116
- Span: `placeholder`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 78
- Span: `PlayState::PLAYING`
- Reason: PlayState::PLAYING could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 121
- Span: `LoopingMode::RESTART`
- Reason: LoopingMode::RESTART could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 4. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 124
- Span: `LoopingMode::AUTO_REVERSE`
- Reason: LoopingMode::AUTO_REVERSE could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 5. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 133
- Span: `StopBehavior::CURRENT_FRAME`
- Reason: StopBehavior::CURRENT_FRAME could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 6. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 136
- Span: `StopBehavior::FIRST_FRAME`
- Reason: StopBehavior::FIRST_FRAME could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 7. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 139
- Span: `StopBehavior::LAST_FRAME`
- Reason: StopBehavior::LAST_FRAME could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 8. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 267
- Span: `ReleasePolicy::DETACHED`
- Reason: ReleasePolicy::DETACHED could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 9. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 270
- Span: `ReleasePolicy::DESTROYED`
- Reason: ReleasePolicy::DESTROYED could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 10. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 273
- Span: `ReleasePolicy::NEVER`
- Reason: ReleasePolicy::NEVER could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 11. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: lottie-animation-view_signals
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/lottie-animation-view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/lottie-animation-view.md`
