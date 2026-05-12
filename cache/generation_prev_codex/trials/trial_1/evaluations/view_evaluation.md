# view Evaluation - Trial 1

## Summary

- Status: needs_review
- Publish ready: true
- Bugs: 0
- Warnings: 0
- Needs review: 2
- Repairable issues: 0

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_SCOPED_SYMBOL | 2 |

## Human Review

### 1. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 151
- Span: `Dali::Ui::Extents`
- Reason: Dali::Ui::Extents could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 254
- Span: `Dali::Ui::InteractiveTrait::ClickedSignalType`
- Reason: Dali::Ui::InteractiveTrait::ClickedSignalType could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/view.md`
