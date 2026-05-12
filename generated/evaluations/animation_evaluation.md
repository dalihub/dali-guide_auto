# animation Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 3
- Warnings: 1
- Needs review: 0
- Repairable issues: 4

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 1 |
| ENUM_VALUE_OWNER_MISMATCH | 1 |
| UNKNOWN_RECEIVER_METHOD | 2 |

## Human Review

### 1. EMPTY_SECTION (warning)

- Location: line 234
- Span: ``
- Reason: Section ## Looping and Speed is empty.

### 2. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 98
- Span: `fadeInSpec.ApplyTo(`
- Reason: fadeInSpec.ApplyTo() does not resolve on inferred type Dali::Ui::View.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 3. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 102
- Span: `fadeInSpec.ApplyTo(`
- Reason: fadeInSpec.ApplyTo() does not resolve on inferred type Dali::Ui::View.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 4. ENUM_VALUE_OWNER_MISMATCH (bug)

- Location: line 168
- Span: `View::Property::CORNER_RADIUS`
- Reason: View::Property::CORNER_RADIUS is not defined by that Property owner.
- Suggested direction: Use the property enum owner that actually defines this value.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/animation.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/animation.md`
