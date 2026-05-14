# image Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 3
- Warnings: 2
- Needs review: 0
- Repairable issues: 5

## Issue Counts

| Type | Count |
|---|---:|
| SCOPED_VALUE_OWNER_MISMATCH | 3 |
| THIN_SECTION | 2 |

## Human Review

### 1. THIN_SECTION (warning)

- Location: line 74
- Span: `## Loading Policies`
- Reason: Section ## Loading Policies has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 2. THIN_SECTION (warning)

- Location: line 121
- Span: `## Animated Images`
- Reason: Section ## Animated Images has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 3. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 265
- Span: `Ui::Visual::ResourceStatus::LOADED`
- Reason: Ui::Visual::ResourceStatus::LOADED could not be resolved with that exact owner/value combination.
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 4. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 305
- Span: `Ui::Visual::ResourceStatus::LOADING`
- Reason: Ui::Visual::ResourceStatus::LOADING could not be resolved with that exact owner/value combination.
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 5. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 309
- Span: `Ui::Visual::ResourceStatus::LOADED`
- Reason: Ui::Visual::ResourceStatus::LOADED could not be resolved with that exact owner/value combination.
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/image.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/image.md`
