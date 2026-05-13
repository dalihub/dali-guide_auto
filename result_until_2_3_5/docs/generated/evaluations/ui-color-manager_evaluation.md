# ui-color-manager Evaluation - Trial 1

## Summary

- Status: needs_review
- Publish ready: true
- Bugs: 0
- Warnings: 1
- Needs review: 2
- Repairable issues: 1

## Issue Counts

| Type | Count |
|---|---:|
| PREFERRED_USAGE_PATTERN_MISSING | 1 |
| UNKNOWN_SCOPED_SYMBOL | 2 |

## Human Review

### 1. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 113
- Span: `Dali::Ui::ColorCallback::New`
- Reason: Dali::Ui::ColorCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 150
- Span: `Dali::Ui::ColorCallback::New`
- Reason: Dali::Ui::ColorCallback::New could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: ui-color-manager_signals
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/ui-color-manager.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/ui-color-manager.md`
