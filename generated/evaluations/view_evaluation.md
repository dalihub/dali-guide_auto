# view Evaluation - Trial 1

## Summary

- Status: needs_review
- Publish ready: true
- Bugs: 0
- Warnings: 1
- Needs review: 3
- Repairable issues: 1

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 1 |
| UNKNOWN_SCOPED_SYMBOL | 3 |

## Human Review

### 1. EMPTY_SECTION (warning)

- Location: line 172
- Span: ``
- Reason: Section ## Visual Styling is empty.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 118
- Span: `Dali::LayoutDirection::RIGHT_TO_LEFT`
- Reason: Dali::LayoutDirection::RIGHT_TO_LEFT could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 493
- Span: `ViewState::ENABLED`
- Reason: ViewState::ENABLED could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 4. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 536
- Span: `Dali::Accessibility::Role::BUTTON`
- Reason: Dali::Accessibility::Role::BUTTON could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/view.md`
