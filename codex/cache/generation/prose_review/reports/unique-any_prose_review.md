# Unique Any Prose Review

Changed prose:
- Clarified the overview to name `Dali::Ui::View` attachment workflows instead of the broader phrase "view-attachment workflows".
- Clarified `Dali::Ui::UniqueAny::Get` type matching: the requested template type is decayed before comparison, matching the public template implementation.
- Tightened the post-move guidance to avoid implying that the moved-from wrapper should be used for stored data.
- Clarified `Dali::Ui::AttachmentId` guidance by naming `Dali::Ui::View` attachment APIs: `SetAttachment`, `GetAttachment`, and `DetachAttachment`.
- Corrected the holder-model guidance to say application code cannot access or construct `HolderBase` and `Holder` directly through the public API.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/unique-any.h` shows `UniqueAny` is move-only, owns one value, stores `Dali::DecayType<T>::type`, supports move-only rvalues, and implements `Get`, `Reset`, `Detach`, and `operator bool`.
- `repos/dali-ui/dali-ui-foundation/public-api/unique-any.cpp` confirms move construction and move assignment transfer `mHolder` and clear the source wrapper.
- `repos/dali-ui/dali-ui-foundation/public-api/attachment-id.h` documents one `AttachmentId` per logical attachment type, static storage reuse, and avoiding per-call allocation.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` documents typed `Dali::Ui::View` attachment APIs and their use of `UniqueAny` with `Dali::UniquePtr<T>`.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-UniqueAny.cpp` verifies allocation uniqueness, `Get`, `Reset`, move-only storage, `Detach`, and attachment-container behavior.

Remaining concerns:
- Several later code snippets assume the earlier `PanelState` example is already in scope. This preserves the draft structure and examples, but the snippets are not fully standalone.
- The guide intentionally keeps the existing "Internal Holder Model" section, although `HolderBase` and `Holder` are private implementation details and should normally be minimized for application-developer guidance.
