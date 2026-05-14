# Attachment Id Prose Review

Changed prose:
- Replaced "stable key" with "lightweight identifier for an attachment slot" to match the public `Dali::Ui::AttachmentId` header wording.
- Changed "`Alloc()` returns a new id" to "`Alloc()` returns the next unique id" to reflect the sequential allocation contract.
- Clarified that `Dali::Ui::View::SetAttachment()` takes ownership of non-null `Dali::UniquePtr` data.
- Expanded the returned-pointer lifetime warning for `Dali::Ui::View::GetAttachment()` to include removal, replacement, and view implementation destruction.
- Added explicit API names and backticks for `Dali::Ui::View::GetAttachment()`, `Dali::Ui::View::RemoveAttachment()`, and `Dali::Ui::View::DetachAttachment()`.
- Clarified that typed `Dali::Ui::View::DetachAttachment()` transfers ownership only when the stored type matches.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/attachment-id.h` documents `Dali::Ui::AttachmentId` as a lightweight identifier, says ids are allocated sequentially via `Dali::Ui::AttachmentId::Alloc()`, and recommends one static id per logical attachment type.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` documents `Dali::Ui::View::SetAttachment()` ownership transfer, non-null assertion, replacement behavior, pointer lifetime rules, typed mismatch behavior, and typed detach semantics.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-View.cpp` verifies typed get mismatch returns null, replacement stores the new value, remove returns false after removal, and typed detach mismatch leaves the original attachment in place.
- `repos/dali-ui/samples/attachment/attachment-example.cpp` and `repos/dali-ui/samples/attachment/my-button-example.cpp` support the guide's View-based app examples, static `AttachmentId` reuse, fluent setters, typed attachment access, and custom handle pattern.

Remaining concerns:
- The examples use anonymous-namespace attachment data types, which is valid for same-translation-unit use as shown. For attachments retrieved across translation units, `view.h` recommends a stable namespace-scope type.
