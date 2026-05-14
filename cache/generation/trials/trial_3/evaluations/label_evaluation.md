# label Evaluation - Trial 3

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 4
- Warnings: 1
- Needs review: 0
- Repairable issues: 5

## Issue Counts

| Type | Count |
|---|---:|
| MISSING_TABLE_OF_CONTENTS | 1 |
| MISSING_TITLE_HEADING | 1 |
| NO_CPP_CODE_BLOCK | 1 |
| TOO_FEW_TOPIC_SECTIONS | 1 |
| UNKNOWN_BACKTICK_API_TOKEN | 1 |

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

- Location: line 13
- Span: `void(View, const Dali::String&`
- Reason: Backticked API-like token `void(View, const Dali::String&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 5. NO_CPP_CODE_BLOCK (bug)

- Location: line n/a
- Span: ``
- Reason: Primary feature guide has no C++ code block.
- Suggested direction: Add at least one compact, source-grounded C++ example for the primary feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_3/validation/label.validation.json`
- Source draft: `cache/generation/trials/trial_3/drafts/label.md`
