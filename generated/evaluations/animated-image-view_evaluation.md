# animated-image-view Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 8
- Needs review: 15
- Repairable issues: 9

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 5 |
| PROMPT_LEAKAGE | 1 |
| UNKNOWN_BACKTICK_API_TOKEN | 3 |
| UNKNOWN_SCOPED_SYMBOL | 15 |

## Human Review

### 1. EMPTY_SECTION (warning)

- Location: line 95
- Span: ``
- Reason: Section ## Frame and Speed Control is empty.

### 2. EMPTY_SECTION (warning)

- Location: line 157
- Span: ``
- Reason: Section ## Image Sources and Caching is empty.

### 3. EMPTY_SECTION (warning)

- Location: line 194
- Span: ``
- Reason: Section ## Visual Appearance is empty.

### 4. EMPTY_SECTION (warning)

- Location: line 296
- Span: ``
- Reason: Section ## Loading Configuration is empty.

### 5. EMPTY_SECTION (warning)

- Location: line 353
- Span: ``
- Reason: Section ## Signals is empty.

### 6. PROMPT_LEAKAGE (bug)

- Location: line 180
- Span: `Placeholder`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

### 7. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 65
- Span: `PlayState::PLAYING`
- Reason: PlayState::PLAYING could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 8. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 104
- Span: `StopBehavior::CURRENT_FRAME`
- Reason: StopBehavior::CURRENT_FRAME could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 9. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 107
- Span: `StopBehavior::FIRST_FRAME`
- Reason: StopBehavior::FIRST_FRAME could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 10. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 110
- Span: `StopBehavior::LAST_FRAME`
- Reason: StopBehavior::LAST_FRAME could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 11. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 203
- Span: `FittingMode::FIT_KEEP_ASPECT_RATIO`
- Reason: FittingMode::FIT_KEEP_ASPECT_RATIO could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 12. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 206
- Span: `FittingMode::FILL`
- Reason: FittingMode::FILL could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 13. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 209
- Span: `FittingMode::OVER_FIT_KEEP_ASPECT_RATIO`
- Reason: FittingMode::OVER_FIT_KEEP_ASPECT_RATIO could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 14. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 212
- Span: `FittingMode::CENTER`
- Reason: FittingMode::CENTER could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 15. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 289
- Span: `MaskingType::MASKING_ON_RENDERING`
- Reason: MaskingType::MASKING_ON_RENDERING could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 16. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 292
- Span: `MaskingType::MASKING_ON_LOADING`
- Reason: MaskingType::MASKING_ON_LOADING could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 17. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 305
- Span: `LoadPolicy::IMMEDIATE`
- Reason: LoadPolicy::IMMEDIATE could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 18. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 308
- Span: `LoadPolicy::ATTACHED`
- Reason: LoadPolicy::ATTACHED could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 19. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 319
- Span: `ReleasePolicy::DETACHED`
- Reason: ReleasePolicy::DETACHED could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 20. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 322
- Span: `ReleasePolicy::DESTROYED`
- Reason: ReleasePolicy::DESTROYED could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 21. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 325
- Span: `ReleasePolicy::NEVER`
- Reason: ReleasePolicy::NEVER could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 22. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 44
- Span: `PlayState::STOPPED`
- Reason: Backticked API-like token `PlayState::STOPPED` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 23. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 45
- Span: `PlayState::PLAYING`
- Reason: Backticked API-like token `PlayState::PLAYING` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 24. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 46
- Span: `PlayState::PAUSED`
- Reason: Backticked API-like token `PlayState::PAUSED` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/animated-image-view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/animated-image-view.md`
