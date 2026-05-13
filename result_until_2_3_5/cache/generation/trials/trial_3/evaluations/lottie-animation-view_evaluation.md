# lottie-animation-view Evaluation - Trial 3

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 1
- Needs review: 0
- Repairable issues: 2

## Issue Counts

| Type | Count |
|---|---:|
| PREFERRED_USAGE_PATTERN_MISSING | 1 |
| PROMPT_LEAKAGE | 1 |

## Human Review

### 1. PROMPT_LEAKAGE (bug)

- Location: line 98
- Span: `Placeholder`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

### 2. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: lottie-animation-view_signals
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_3/validation/lottie-animation-view.validation.json`
- Source draft: `cache/generation/trials/trial_3/drafts/lottie-animation-view.md`
