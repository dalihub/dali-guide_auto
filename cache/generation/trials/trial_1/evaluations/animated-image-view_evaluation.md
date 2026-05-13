# animated-image-view Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 17
- Warnings: 4
- Needs review: 0
- Repairable issues: 21

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 4 |
| SCOPED_VALUE_OWNER_MISMATCH | 17 |

## Human Review

### 1. EMPTY_SECTION (warning)

- Location: line 93
- Span: ``
- Reason: Section ## Loop and Speed Configuration is empty.

### 2. EMPTY_SECTION (warning)

- Location: line 175
- Span: ``
- Reason: Section ## Visual Appearance is empty.

### 3. EMPTY_SECTION (warning)

- Location: line 265
- Span: ``
- Reason: Section ## Loading Configuration is empty.

### 4. EMPTY_SECTION (warning)

- Location: line 314
- Span: ``
- Reason: Section ## Signals is empty.

### 5. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 71
- Span: `PlayState::PLAYING`
- Reason: PlayState::PLAYING could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Animation::State::PLAYING`, `Dali::TtsPlayer::State::PLAYING`, `Dali::Ui::AnimatedImage::PlayState::PLAYING`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 6. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 75
- Span: `PlayState::PAUSED`
- Reason: PlayState::PAUSED could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Animation::State::PAUSED`, `Dali::TtsPlayer::State::PAUSED`, `Dali::Ui::AnimatedImage::PlayState::PAUSED`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 7. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 79
- Span: `PlayState::STOPPED`
- Reason: PlayState::STOPPED could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Animation::State::STOPPED`, `Dali::Ui::AnimatedImage::PlayState::STOPPED`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 8. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 141
- Span: `StopBehavior::CURRENT_FRAME`
- Reason: StopBehavior::CURRENT_FRAME could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Ui::AnimatedImage::StopBehavior::CURRENT_FRAME`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 9. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 144
- Span: `StopBehavior::FIRST_FRAME`
- Reason: StopBehavior::FIRST_FRAME could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Ui::AnimatedImage::StopBehavior::FIRST_FRAME`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 10. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 147
- Span: `StopBehavior::LAST_FRAME`
- Reason: StopBehavior::LAST_FRAME could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Ui::AnimatedImage::StopBehavior::LAST_FRAME`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 11. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 184
- Span: `FittingMode::FIT_KEEP_ASPECT_RATIO`
- Reason: FittingMode::FIT_KEEP_ASPECT_RATIO could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 12. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 187
- Span: `FittingMode::FILL`
- Reason: FittingMode::FILL could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Ui::Image::FittingMode::FILL`, `Dali::Ui::LayoutAlignment::FILL`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 13. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 190
- Span: `FittingMode::OVER_FIT_KEEP_ASPECT_RATIO`
- Reason: FittingMode::OVER_FIT_KEEP_ASPECT_RATIO could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 14. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 193
- Span: `FittingMode::CENTER`
- Reason: FittingMode::CENTER could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::HorizontalAlignment::Type::CENTER`, `Dali::ParentOrigin::CENTER`, `Dali::Pivot::CENTER`, `Dali::Ui::Align::Type::CENTER`, `Dali::Ui::FlexAlign::CENTER`, `Dali::Ui::FlexJustify::CENTER`, `Dali::Ui::GradientVisual::Property::::CENTER`, `Dali::Ui::GradientVisual::Property::CENTER`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 15. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 258
- Span: `MaskingType::MASKING_ON_RENDERING`
- Reason: MaskingType::MASKING_ON_RENDERING could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 16. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 261
- Span: `MaskingType::MASKING_ON_LOADING`
- Reason: MaskingType::MASKING_ON_LOADING could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Ui::Image::MaskingType::MASKING_ON_LOADING`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 17. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 274
- Span: `LoadPolicy::IMMEDIATE`
- Reason: LoadPolicy::IMMEDIATE could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Ui::Image::LoadPolicy::IMMEDIATE`, `Dali::Ui::Text::MarqueeStopMode::IMMEDIATE`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 18. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 277
- Span: `LoadPolicy::ATTACHED`
- Reason: LoadPolicy::ATTACHED could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Ui::Image::LoadPolicy::ATTACHED`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 19. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 288
- Span: `ReleasePolicy::DETACHED`
- Reason: ReleasePolicy::DETACHED could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Ui::Image::ReleasePolicy::DETACHED`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 20. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 291
- Span: `ReleasePolicy::DESTROYED`
- Reason: ReleasePolicy::DESTROYED could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Ui::Image::ReleasePolicy::DESTROYED`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 21. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 294
- Span: `ReleasePolicy::NEVER`
- Reason: ReleasePolicy::NEVER could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::DepthFunction::Type::NEVER`, `Dali::StencilFunction::Type::NEVER`, `Dali::Ui::Image::ReleasePolicy::NEVER`, `Dali::WebEngineCookieManager::CookieAcceptPolicy::NEVER`, `Dali::WindowScreenOffMode::NEVER`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/animated-image-view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/animated-image-view.md`
