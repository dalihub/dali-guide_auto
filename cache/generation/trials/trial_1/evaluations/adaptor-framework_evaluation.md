# adaptor-framework Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 5
- Warnings: 10
- Needs review: 0
- Repairable issues: 15

## Issue Counts

| Type | Count |
|---|---:|
| SCOPED_VALUE_OWNER_MISMATCH | 2 |
| THIN_SECTION | 9 |
| UNKNOWN_BACKTICK_API_TOKEN | 1 |
| UNKNOWN_RECEIVER_METHOD | 3 |

## Human Review

### 1. THIN_SECTION (warning)

- Location: line 23
- Span: `## Application Lifecycle`
- Reason: Section ## Application Lifecycle has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 2. THIN_SECTION (warning)

- Location: line 145
- Span: `## Window Management`
- Reason: Section ## Window Management has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 3. THIN_SECTION (warning)

- Location: line 235
- Span: `## Device Orientation`
- Reason: Section ## Device Orientation has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 4. THIN_SECTION (warning)

- Location: line 279
- Span: `## Input Method Context`
- Reason: Section ## Input Method Context has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 5. THIN_SECTION (warning)

- Location: line 355
- Span: `## Async Task Processing`
- Reason: Section ## Async Task Processing has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 6. THIN_SECTION (warning)

- Location: line 412
- Span: `## Timer and Scheduling`
- Reason: Section ## Timer and Scheduling has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 7. THIN_SECTION (warning)

- Location: line 461
- Span: `## Clipboard Operations`
- Reason: Section ## Clipboard Operations has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 8. THIN_SECTION (warning)

- Location: line 505
- Span: `## Drag and Drop`
- Reason: Section ## Drag and Drop has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 9. THIN_SECTION (warning)

- Location: line 564
- Span: `## Feedback and Haptics`
- Reason: Section ## Feedback and Haptics has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 10. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 332
- Span: `settings.Insert(`
- Reason: settings.Insert() does not resolve on inferred type Dali::Property::Map.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 11. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 333
- Span: `settings.Insert(`
- Reason: settings.Insert() does not resolve on inferred type Dali::Property::Map.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 12. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 334
- Span: `settings.Insert(`
- Reason: settings.Insert() does not resolve on inferred type Dali::Property::Map.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 13. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 367
- Span: `PriorityType::DEFAULT`
- Reason: PriorityType::DEFAULT could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::AlphaFunction::BuiltinFunction::DEFAULT`, `Dali::AsyncTask::PriorityType::DEFAULT`, `Dali::AsyncTaskManager::CompletedCallbackTraceMask::DEFAULT`, `Dali::EncodedImageBuffer::ImageType::DEFAULT`, `Dali::FilterMode::Type::DEFAULT`, `Dali::Graphics::Backend::DEFAULT`, `Dali::InputMethod::ButtonAction::Type::DEFAULT`, `Dali::InputMethodContext::State::DEFAULT`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 14. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 367
- Span: `ThreadType::MAIN_THREAD`
- Reason: ThreadType::MAIN_THREAD could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::AsyncTask::ThreadType::MAIN_THREAD`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 15. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 119
- Span: `Application::GetOrientation`
- Reason: Backticked API-like token `Application::GetOrientation` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/adaptor-framework.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/adaptor-framework.md`
