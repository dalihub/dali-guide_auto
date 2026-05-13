# adaptor-framework Evaluation - Trial 1

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

- Location: line 175
- Span: `Dali::DeviceStatus::Battery::CRITICALLY_LOW`
- Reason: Dali::DeviceStatus::Battery::CRITICALLY_LOW could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 183
- Span: `Dali::DeviceStatus::Memory::CRITICALLY_LOW`
- Reason: Dali::DeviceStatus::Memory::CRITICALLY_LOW could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/adaptor-framework.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/adaptor-framework.md`
