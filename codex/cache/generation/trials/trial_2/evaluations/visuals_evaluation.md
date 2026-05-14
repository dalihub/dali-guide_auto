# visuals Evaluation - Trial 2

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

- Location: line 284
- Span: `range.Add(`
- Reason: range.Add() does not resolve on inferred type Dali::Property::Array.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 2. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 285
- Span: `range.Add(`
- Reason: range.Add() does not resolve on inferred type Dali::Property::Array.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_2/validation/visuals.validation.json`
- Source draft: `cache/generation/trials/trial_2/drafts/visuals.md`
