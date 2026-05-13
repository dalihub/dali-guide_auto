# render-effects Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 4
- Warnings: 0
- Needs review: 0
- Repairable issues: 4

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_RECEIVER_METHOD | 4 |

## Human Review

### 1. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 33
- Span: `effect.SetBlurDownscaleFactor(`
- Reason: effect.SetBlurDownscaleFactor() does not resolve on inferred type Dali::Ui::MaskEffect.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 2. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 34
- Span: `effect.SetBlurOnce(`
- Reason: effect.SetBlurOnce() does not resolve on inferred type Dali::Ui::MaskEffect.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 3. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 41
- Span: `effect.SetBlurDownscaleFactor(`
- Reason: effect.SetBlurDownscaleFactor() does not resolve on inferred type Dali::Ui::MaskEffect.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 4. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 42
- Span: `effect.SetBlurOnce(`
- Reason: effect.SetBlurOnce() does not resolve on inferred type Dali::Ui::MaskEffect.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/render-effects.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/render-effects.md`
