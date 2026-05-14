# lottie-animation-view Evaluation - Trial 3

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
| UNKNOWN_RECEIVER_METHOD | 2 |

## Human Review

### 1. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 367
- Span: `contentInfo.Count(`
- Reason: contentInfo.Count() does not resolve on inferred type Dali::Property::Map.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 2. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 378
- Span: `markerInfo.Count(`
- Reason: markerInfo.Count() does not resolve on inferred type Dali::Property::Map.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_3/validation/lottie-animation-view.validation.json`
- Source draft: `cache/generation/trials/trial_3/drafts/lottie-animation-view.md`
