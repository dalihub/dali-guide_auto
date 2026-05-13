# image-loader Evaluation - Trial 2

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 4
- Warnings: 5
- Needs review: 0
- Repairable issues: 9

## Issue Counts

| Type | Count |
|---|---:|
| MISSING_TABLE_OF_CONTENTS | 1 |
| MISSING_TITLE_HEADING | 1 |
| NO_CPP_CODE_BLOCK | 1 |
| PREFERRED_USAGE_PATTERN_MISSING | 2 |
| TOO_FEW_TOPIC_SECTIONS | 1 |
| UNKNOWN_BACKTICK_API_TOKEN | 3 |

## Human Review

### 1. MISSING_TITLE_HEADING (bug)

- Location: line n/a
- Span: ``
- Reason: Markdown draft has no top-level title heading.

### 2. MISSING_TABLE_OF_CONTENTS (bug)

- Location: line n/a
- Span: ``
- Reason: Markdown draft has no table of contents heading.

### 3. TOO_FEW_TOPIC_SECTIONS (bug)

- Location: line n/a
- Span: ``
- Reason: Only 0 topic sections found; expected at least 2.

### 4. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 4
- Span: `Dali::Toolkit::Visual::Property::TYPE`
- Reason: Backticked API-like token `Dali::Toolkit::Visual::Property::TYPE` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 5. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 5
- Span: `Dali::Toolkit::ImageVisual::Property::URL`
- Reason: Backticked API-like token `Dali::Toolkit::ImageVisual::Property::URL` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 6. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 6
- Span: `Dali::Toolkit::Visual::IMAGE`
- Reason: Backticked API-like token `Dali::Toolkit::Visual::IMAGE` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 7. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: image-loader_basic_setup
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

### 8. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: image-loader_signals
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

### 9. NO_CPP_CODE_BLOCK (bug)

- Location: line n/a
- Span: ``
- Reason: Primary feature guide has no C++ code block.
- Suggested direction: Add at least one compact, source-grounded C++ example for the primary feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_2/validation/image-loader.validation.json`
- Source draft: `cache/generation/trials/trial_2/drafts/image-loader.md`
