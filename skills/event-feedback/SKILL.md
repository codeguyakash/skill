---
name: event-feedback
description: Proper user feedback on every event — clicks, submits, saves, deletes, network changes, async operations. Use when building any interactive UI or API response that a user waits on.
---

# Event Feedback

Every user action must produce a visible, immediate, accurate response. No silent clicks.

## The feedback loop
1. **Acknowledge instantly (<100ms)** — pressed state, spinner, disabled button.
2. **Show progress** for operations over ~1s (skeleton, progress bar, status text).
3. **Confirm the outcome** — success toast/inline message, or error with a next step.
4. **Make it recoverable** — retry, undo, or a clear path forward.

## Pattern by event
| Event | Feedback |
|---|---|
| Form submit | Disable submit, show spinner in button, prevent double-submit; on success toast + redirect/reset; on error inline field errors + summary |
| Save / update | "Saving…" → "Saved" (auto-clears); on failure keep user input and offer Retry |
| Delete | Confirm dialog naming the item; then toast with **Undo** (soft delete if possible) |
| Page / data load | Skeleton matching final layout; error state with Retry; empty state with a call to action |
| Navigation | Route progress bar; keep old view until new is ready |
| Copy | "Copied" for 2s |
| File upload | Per-file progress, cancel, error per file |
| Network lost | Persistent offline banner; queue or block writes; "Back online" toast |
| Permission denied | Explain why and what to do (log in, request access) |
| Long job (>10s) | Background it, show status, notify on completion |

## Messages
- **Success:** say what happened. `Profile updated`, not `Success!`.
- **Error:** what happened + why (if known) + what to do. `Couldn't save changes. Check your connection and try again.`
- **Never** show raw errors, codes, or stack traces to users. Show a reference ID for support.
- Plain language. No blame ("You entered an invalid…"). No emoji in UI copy.
- Toasts: auto-dismiss success (3-5s), keep errors until dismissed, `role="status"` / `aria-live="polite"` (errors `role="alert"`).

## Forms
- Validate on blur and on submit; show error under the field, linked with `aria-describedby`.
- Focus the first invalid field on failed submit.
- Preserve input on error. Never clear the form on failure.
- Disable submit while pending, not while invalid (so users can see why).

## Implementation pattern (React)
```tsx
type Status = 'idle' | 'pending' | 'success' | 'error';

function SaveButton({ onSave }: { onSave: () => Promise<void> }) {
  const [status, setStatus] = useState<Status>('idle');

  const handleClick = async () => {
    if (status === 'pending') return;
    setStatus('pending');
    try {
      await onSave();
      setStatus('success');
      toast.success('Changes saved');
    } catch (error) {
      setStatus('error');
      reportError(error);
      toast.error("Couldn't save changes. Try again.", { action: { label: 'Retry', onClick: handleClick } });
    }
  };

  return (
    <button onClick={handleClick} disabled={status === 'pending'} aria-busy={status === 'pending'}>
      {status === 'pending' ? 'Saving…' : 'Save'}
    </button>
  );
}
```

## Optimistic UI
Use for low-risk, high-success actions (like, toggle, reorder). Update instantly, roll back and notify on failure.

## API feedback
Return machine-readable `code`, human `message`, field-level `details`, and `requestId`. Use proper status codes so the UI can map them to the right message.

## Checklist
- [ ] Every button/submit has pending, success, and error behavior
- [ ] No double-submit possible
- [ ] Loading, empty, error states for every data view
- [ ] Destructive actions confirmed or undoable
- [ ] Announced to screen readers
- [ ] Offline handled
