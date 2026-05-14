# view Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 5
- Warnings: 9
- Needs review: 0
- Repairable issues: 14

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 9 |
| SCOPED_VALUE_OWNER_MISMATCH | 4 |
| UNKNOWN_RECEIVER_METHOD | 1 |

## Human Review

### 1. EMPTY_SECTION (warning)

- Location: line 40
- Span: ``
- Reason: Section ## Layout and Sizing is empty.

### 2. EMPTY_SECTION (warning)

- Location: line 111
- Span: ``
- Reason: Section ## Visual Styling is empty.

### 3. EMPTY_SECTION (warning)

- Location: line 169
- Span: ``
- Reason: Section ## View Hierarchy is empty.

### 4. EMPTY_SECTION (warning)

- Location: line 218
- Span: ``
- Reason: Section ## Input and Interaction is empty.

### 5. EMPTY_SECTION (warning)

- Location: line 286
- Span: ``
- Reason: Section ## Focus Navigation is empty.

### 6. EMPTY_SECTION (warning)

- Location: line 332
- Span: ``
- Reason: Section ## State Management is empty.

### 7. EMPTY_SECTION (warning)

- Location: line 370
- Span: ``
- Reason: Section ## Animation is empty.

### 8. EMPTY_SECTION (warning)

- Location: line 409
- Span: ``
- Reason: Section ## Selection with SelectableTrait is empty.

### 9. EMPTY_SECTION (warning)

- Location: line 479
- Span: ``
- Reason: Section ## Utility Methods is empty.

### 10. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 353
- Span: `ViewState::ENABLED`
- Reason: ViewState::ENABLED could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Accessibility::Bridge::AutoInitState::ENABLED`, `Dali::Accessibility::State::ENABLED`, `Dali::Ui::AccessibilityState::ENABLED`, `Dali::Ui::UiScalePolicy::ENABLED`, `Dali::VideoSyncMode::ENABLED`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 11. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 393
- Span: `fadeInSpec.ApplyTo(`
- Reason: fadeInSpec.ApplyTo() does not resolve on inferred type Dali::Ui::View.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 12. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 467
- Span: `AttachmentId::USER_DATA`
- Reason: AttachmentId::USER_DATA could not be resolved with that exact owner/value combination.
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 13. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 469
- Span: `AttachmentId::USER_DATA`
- Reason: AttachmentId::USER_DATA could not be resolved with that exact owner/value combination.
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 14. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 475
- Span: `AttachmentId::USER_DATA`
- Reason: AttachmentId::USER_DATA could not be resolved with that exact owner/value combination.
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/view.md`
