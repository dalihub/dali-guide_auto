# Preprocess Review

This report validates preprocess inputs before markdown generation.

## Summary

- info: 2
- warning: 33

## Warning

- [warning] PLANNING_AMBIGUOUS_RAW_FEATURE: Raw ambiguous feature is not a document target. (uncategorized_ambiguous_root)
  - action: Improve parser/path inference or suppress/merge the raw ambiguous feature.
- [warning] PLANNING_MISSING_ENTRY_HEADERS: Document feature has no app entry headers. (adaptor-framework)
  - action: Check whether the feature is truly app-facing or should be supporting/context only.
- [warning] PLANNING_MISSING_ENTRY_HEADERS: Document feature has no app entry headers. (addon-manager)
  - action: Check whether the feature is truly app-facing or should be supporting/context only.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (align-enumerations)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (callback)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (common)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (core-enumerations)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (custom-actor)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (direction-enums)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (drawable-actor)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (dummy-component)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (dummy-component.autogen)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (interactive-event-receiver-interface)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (processor-interface)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (property-bridge)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (size-negotiation)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (text-visualizer-properties)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (theme-loader-interface)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] PLANNING_UNRESOLVED_SUPPORTING_OWNER: Supporting or suppressed feature has no confident owner document feature. (trait-interface)
  - action: Add a general ownership rule or reviewed override if this feature should be included.
- [warning] UNKNOWN_PLANNER_ROLE: Role inference rule emits a role without a configured section hint. (role:impl)
  - action: Add the role to role_section_hints or fix the role inference rule.
- [warning] UNKNOWN_PLANNER_ROLE: Role inference rule emits a role without a configured section hint. (role:interface)
  - action: Add the role to role_section_hints or fix the role inference rule.
- [warning] NO_SAMPLE_EVIDENCE: No matching sample path was found for this feature. (accessibility-highlight-overlay)
  - action: This may be acceptable; add samples or improve sample matching if examples are needed.
- [warning] MISSING_ENTRY_HEADER: feature_spec has no entry header. (adaptor-framework)
  - action: Review document_plan entry header selection.
- [warning] MISSING_ENTRY_HEADER: feature_spec has no entry header. (addon-manager)
  - action: Review document_plan entry header selection.
- [warning] NO_SAMPLE_EVIDENCE: No matching sample path was found for this feature. (addon-manager)
  - action: This may be acceptable; add samples or improve sample matching if examples are needed.
- [warning] NO_SAMPLE_EVIDENCE: No matching sample path was found for this feature. (i-scroll-bar)
  - action: This may be acceptable; add samples or improve sample matching if examples are needed.
- [warning] NO_SAMPLE_EVIDENCE: No matching sample path was found for this feature. (image-loader)
  - action: This may be acceptable; add samples or improve sample matching if examples are needed.
- [warning] NO_SAMPLE_EVIDENCE: No matching sample path was found for this feature. (state-event)
  - action: This may be acceptable; add samples or improve sample matching if examples are needed.
- [warning] NO_SAMPLE_EVIDENCE: No matching sample path was found for this feature. (trait-id)
  - action: This may be acceptable; add samples or improve sample matching if examples are needed.
- [warning] NO_SAMPLE_EVIDENCE: No matching sample path was found for this feature. (ui-color-manager)
  - action: This may be acceptable; add samples or improve sample matching if examples are needed.
- [warning] NO_SAMPLE_EVIDENCE: No matching sample path was found for this feature. (ui-component-config)
  - action: This may be acceptable; add samples or improve sample matching if examples are needed.
- [warning] NO_SAMPLE_EVIDENCE: No matching sample path was found for this feature. (ui-theme-manager)
  - action: This may be acceptable; add samples or improve sample matching if examples are needed.
- [warning] NO_SAMPLE_EVIDENCE: No matching sample path was found for this feature. (view-state)
  - action: This may be acceptable; add samples or improve sample matching if examples are needed.

## Decisions

- accessibility-highlight-overlay: category=accessibility (llm_category_classifier)
- adaptor-framework: category=application-framework (llm_category_classifier)
- addon-manager: category=platform-integration (llm_category_classifier)
- animated-image-view: category=views-components (llm_category_classifier)
- animation: category=animation-motion (category_override)
- attachment-id: category=utilities (llm_category_classifier)
- focus-manager: category=input-interaction (llm_category_classifier)
- i-scroll-bar: category=views-components (llm_category_classifier)
- image: category=images-visuals (llm_category_classifier)
- image-loader: category=images-visuals (llm_category_classifier)
- image-view: category=views-components (category_override)
- input-event: category=input-interaction (llm_category_classifier)
- input-field: category=views-components (llm_category_classifier)
- interactive-view: category=views-components (llm_category_classifier)
- label: category=views-components (category_override)
- layouts: category=layout-sizing (llm_category_classifier)
- lottie-animation-view: category=views-components (llm_category_classifier)
- render-effects: category=images-visuals (llm_category_classifier)
- scroll-bar: category=views-components (llm_category_classifier)
- scroll-view: category=views-components (llm_category_classifier)
- signals: category=utilities (llm_category_classifier)
- state-event: category=input-interaction (llm_category_classifier)
- text: category=text (llm_category_classifier)
- trait-id: category=utilities (llm_category_classifier)
- ui-color: category=styling-theme-config (llm_category_classifier)
- ui-color-manager: category=styling-theme-config (llm_category_classifier)
- ui-component-config: category=styling-theme-config (llm_category_classifier)
- ui-config: category=styling-theme-config (llm_category_classifier)
- ui-scale-manager: category=styling-theme-config (llm_category_classifier)
- ui-theme-manager: category=styling-theme-config (llm_category_classifier)
- unique-any: category=utilities (llm_category_classifier)
- view: category=views-components (category_override)
- view-state: category=views-components (llm_category_classifier)
- visuals: category=images-visuals (llm_category_classifier)
- web-view: category=views-components (llm_category_classifier)
- <global>: llm_usage category_requests=1 ownership_requests=9 estimated_input_tokens=57180 (document_plan_llm_usage)
- <global>: llm_trace entries=10 (document_plan_llm_trace)
- actors -> view: document_feature_rules.merge_into -> view (supporting_features)
- align-enumerations: document_feature_rules.suppress ->  (supporting_features)
- callback: document_feature_rules.suppress ->  (supporting_features)
- camera-view -> view: document_feature_rules.suppress -> view (supporting_features)
- common: document_feature_rules.suppress ->  (supporting_features)
- custom-actor: document_feature_rules.suppress ->  (supporting_features)
- drawable-actor: document_feature_rules.suppress ->  (supporting_features)
- dummy-component: document_feature_rules.suppress ->  (supporting_features)
- image-view-types -> image-view: document_feature_rules.merge_into -> image-view (supporting_features)
- interactive-trait -> view: document_feature_rules.merge_into -> view (supporting_features)
- selectable-trait -> view: document_feature_rules.merge_into -> view (supporting_features)
- size-negotiation: document_feature_rules.suppress ->  (supporting_features)
- trait -> view: document_feature_rules.merge_into -> view (supporting_features)
- ui-state -> view: document_feature_rules.merge_into -> view (supporting_features)
- view-impl -> view: document_feature_rules.extension_surfaces -> view (supporting_features)

## Follow-Up Notes

- `document_feature_rules` in `doc_config.yaml` is the canonical location for document ownership, suppress, merge, and extension-surface policy.
- Context feature extraction is intentionally separated from Phase 1.6; feature-specific hardcoded context fallbacks should not be reintroduced.
