# lottie-animation-view Evaluation - Trial 5

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 2
- Warnings: 0
- Needs review: 0
- Repairable issues: 2

## Issue Counts

| Type | Count |
|---|---:|
| ENUM_VALUE_OWNER_MISMATCH | 2 |

## Human Review

### 1. ENUM_VALUE_OWNER_MISMATCH (bug)

- Location: line 367
- Span: `Dali::Property::NONE`
- Reason: Dali::Property::NONE is not defined by that Property owner.
- Suggested direction: Use the property enum owner that actually defines this value.

### 2. ENUM_VALUE_OWNER_MISMATCH (bug)

- Location: line 378
- Span: `Dali::Property::NONE`
- Reason: Dali::Property::NONE is not defined by that Property owner.
- Suggested direction: Use the property enum owner that actually defines this value.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_5/validation/lottie-animation-view.validation.json`
- Source draft: `cache/generation/trials/trial_5/drafts/lottie-animation-view.md`
