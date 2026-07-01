# Forms

Forms have a high density of correctness bugs in React. Review for controlled/uncontrolled mixing, submission races, and validation ownership.

## Controlled vs Uncontrolled Inputs

A controlled input has `value` driven by state and an `onChange` handler. An uncontrolled input uses `defaultValue` and lets the DOM own the value. Mixing them on the same element is invalid and produces a React warning that most engineers dismiss — but the behavior is unpredictable.

```tsx
// Invalid: mixing controlled (value) and uncontrolled (defaultValue)
<input value={value || ''} defaultValue="placeholder" />

// Missing onChange makes a controlled input read-only (React warns)
<input value={name} />

// Correct controlled
<input value={name} onChange={e => setName(e.target.value)} />

// Correct uncontrolled
<input defaultValue="placeholder" ref={inputRef} />
```

File inputs are always uncontrolled (`value` cannot be set programmatically). To reset a file input, change its `key` prop.

## Double-Submit

If an async submit handler does not disable the submit trigger during the inflight request, the user can dispatch duplicate mutations by clicking twice or pressing Enter rapidly. This is a correctness bug on any form that creates or modifies data.

```tsx
// Bug: no disabled state during inflight request
<button type="submit" onClick={handleSubmit}>Save</button>

// Fix: disable during pending state
const [isPending, setIsPending] = useState(false);

async function handleSubmit() {
  setIsPending(true);
  try {
    await saveData(formData);
  } finally {
    setIsPending(false);
  }
}

<button type="submit" onClick={handleSubmit} disabled={isPending}>
  {isPending ? 'Saving...' : 'Save'}
</button>
```

In Next.js with Server Actions, use `useFormStatus` or `useActionState` to derive the pending state from the framework rather than managing it manually.

## Validation Timing

Validation timing has direct UX consequences. Flag validation strategies that are inconsistent with the user flow:

- **On every keystroke**: appropriate for real-time feedback (password strength meter), but showing errors before the user has finished typing is disruptive for most fields
- **On blur**: the most common and generally correct approach for individual field validation
- **On submit only**: acceptable for simple forms; can feel unresponsive on long forms where the user must scroll back to find errors

The most common bug is validating on mount: an empty form that shows all fields as invalid before the user has typed anything. Ensure validation state is not initialized from the invalid initial values.

## Error State and Accessibility

After a failed form submission, the user must be able to find and understand the errors:

- Focus should move to an error summary or the first invalid field
- Each invalid field should have `aria-invalid="true"` and `aria-describedby` pointing to its error message
- Error messages should be associated with their field by id, not just visually placed nearby

```tsx
<div>
  <label htmlFor="email">Email</label>
  <input
    id="email"
    type="email"
    aria-invalid={!!errors.email}
    aria-describedby={errors.email ? 'email-error' : undefined}
  />
  {errors.email && (
    <span id="email-error" role="alert">{errors.email}</span>
  )}
</div>
```

## Optimistic Updates and Rollback

Forms that apply optimistic UI updates before the server confirms must have a rollback path for failures. Review whether the component restores state on error or leaves the UI in an incorrect optimistic state after a failed mutation.

## Schema Validation

Business logic for what constitutes a valid form submission should not be implemented twice — once on the client for UX and once on the server for security. Prefer a shared schema (e.g., zod) that can validate on both sides.

Flag forms that validate on the client but perform no server-side validation of the inputs before persisting them.
