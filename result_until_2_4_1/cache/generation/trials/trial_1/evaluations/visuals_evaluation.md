# visuals Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 2
- Needs review: 0
- Repairable issues: 3

## Issue Counts

| Type | Count |
|---|---:|
| SCOPED_VALUE_OWNER_MISMATCH | 1 |
| UNKNOWN_BACKTICK_API_TOKEN | 2 |

## Human Review

### 1. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 154
- Span: `Dali::Ui::CutoutPolicy::CUTOUT_OUTSIDE_WITH_CORNER_RADIUS`
- Reason: Dali::Ui::CutoutPolicy::CUTOUT_OUTSIDE_WITH_CORNER_RADIUS could not be resolved with that exact owner/value combination.
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 52
- Span: `Dali::Ui::CutoutPolicy::CUTOUT_VIEW_WITH_CORNER_RADIUS`
- Reason: Backticked API-like token `Dali::Ui::CutoutPolicy::CUTOUT_VIEW_WITH_CORNER_RADIUS` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 3. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 52
- Span: `Dali::Ui::CutoutPolicy::CUTOUT_OUTSIDE_WITH_CORNER_RADIUS`
- Reason: Backticked API-like token `Dali::Ui::CutoutPolicy::CUTOUT_OUTSIDE_WITH_CORNER_RADIUS` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/visuals.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/visuals.md`
