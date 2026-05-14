# Attachment Id Prose Review

## Summary

The draft is accurate and well-aligned with the public API. One minor improvement was made to document the return value of `RemoveAttachment()`.

## Changes Made

### 1. RemoveAttachment return value documentation

**Location:** "Detaching and Reattaching" section

**Before:**
> Use `View::RemoveAttachment()` to destroy an attachment without retrieving it:
> ```cpp
> view.RemoveAttachment(COUNTER_DATA_ID);
> ```

**After:**
> Use `View::RemoveAttachment()` to destroy an attachment without retrieving it. It returns `true` if an attachment was removed, or `false` if no attachment existed for the given ID:
> ```cpp
> bool removed = view.RemoveAttachment(COUNTER_DATA_ID);
> ```

**Source Evidence:** `repos/dali-ui/dali-ui-foundation/public-api/view.h` line 1018-1024:
```cpp
/**
 * @brief Removes an attachment.
 *
 * Removing an attachment destroys the stored UniqueAny and its owned value.
 *
 * @param[in] id The key to identify the attachment
 * @return True if an attachment was removed
 */
bool RemoveAttachment(AttachmentId id);
```

## Verified Accurate Prose

The following sections were verified against source and required no changes:

1. **AttachmentId struct description** - Matches `repos/dali-ui/dali-ui-foundation/public-api/attachment-id.h` documentation
2. **Alloc() thread-safety** - Header confirms "Thread-safe and lock-free" with "internal atomic counter"
3. **ID space exhaustion behavior** - Header confirms "assertion is raised instead of wrapping around"
4. **SetAttachment ownership transfer** - Header confirms `Dali::UniquePtr<T>` ownership transfer and replacement behavior
5. **GetAttachment return semantics** - Header confirms pointer ownership remains with View, returns nullptr on missing/type mismatch
6. **DetachAttachment return semantics** - Header confirms returns `Dali::UniquePtr<T>`, nullptr on missing/type mismatch
7. **Comparison operators** - Header confirms `operator==` and `operator!=` compare `value` members
8. **value member** - Header confirms `uint32_t value` is a public member

## Code Block Verification

All code blocks were verified against:
- `repos/dali-ui/dali-ui-foundation/public-api/attachment-id.h`
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`
- `repos/dali-ui/samples/attachment/attachment-example.cpp`

Code patterns match actual usage in the sample code.

## Remaining Concerns

None. The document accurately describes the AttachmentId feature for application developers.
