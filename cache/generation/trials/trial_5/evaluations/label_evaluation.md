# label Evaluation - Trial 5

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 5
- Warnings: 2
- Needs review: 0
- Repairable issues: 7

## Issue Counts

| Type | Count |
|---|---:|
| MISSING_TABLE_OF_CONTENTS | 1 |
| MISSING_TITLE_HEADING | 1 |
| NO_CPP_CODE_BLOCK | 1 |
| PREFERRED_USAGE_PATTERN_MISSING | 2 |
| PROMPT_LEAKAGE | 1 |
| TOO_FEW_TOPIC_SECTIONS | 1 |

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

### 4. PROMPT_LEAKAGE (bug)

- Location: line 17
- Span: `context pack`
- Reason: Prompt/internal wording leaked into the document: context pack
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

### 5. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: label_basic_setup
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

### 6. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: label_signals
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

### 7. NO_CPP_CODE_BLOCK (bug)

- Location: line n/a
- Span: ``
- Reason: Primary feature guide has no C++ code block.
- Suggested direction: Add at least one compact, source-grounded C++ example for the primary feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_5/validation/label.validation.json`
- Source draft: `cache/generation/trials/trial_5/drafts/label.md`
