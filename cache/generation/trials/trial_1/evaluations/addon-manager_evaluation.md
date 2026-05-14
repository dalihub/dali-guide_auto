# addon-manager Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 2
- Warnings: 5
- Needs review: 0
- Repairable issues: 7

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 1 |
| THIN_SECTION | 3 |
| UNKNOWN_BACKTICK_API_TOKEN | 1 |
| UNKNOWN_RECEIVER_METHOD | 2 |

## Human Review

### 1. EMPTY_SECTION (warning)

- Location: line 43
- Span: ``
- Reason: Section ## Loading and Using AddOns is empty.

### 2. THIN_SECTION (warning)

- Location: line 112
- Span: `## Retrieving AddOn Information`
- Reason: Section ## Retrieving AddOn Information has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 3. THIN_SECTION (warning)

- Location: line 157
- Span: `## Function Binding with AddOnBinder`
- Reason: Section ## Function Binding with AddOnBinder has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 4. THIN_SECTION (warning)

- Location: line 295
- Span: `## Creating Custom AddOns`
- Reason: Section ## Creating Custom AddOns has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 5. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 185
- Span: `addon.IsValid(`
- Reason: addon.IsValid() does not resolve on inferred type ImageLoaderAddOn.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 6. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 188
- Span: `addon.GetFormatExtension(`
- Reason: addon.GetFormatExtension() does not resolve on inferred type ImageLoaderAddOn.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 7. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 140
- Span: `void(*)(Dali::AddOnInfo&`
- Reason: Backticked API-like token `void(*)(Dali::AddOnInfo&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/addon-manager.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/addon-manager.md`
