# Examples

Use examples to calibrate review tone. Strong comments identify a concrete issue, explain why it matters, and suggest the smallest useful improvement.

## Strong Comment: Derived State

**Issue**: `filteredUsers` is stored separately from `users` and `query`.

**Reasoning**: This creates two sources of truth. If `users` refreshes while `query` stays the same, the rendered list can become stale unless every update path remembers to recalculate `filteredUsers`.

**Suggested improvement**: Derive `filteredUsers` during render from `users` and `query`, or memoize only if profiling shows the filter is expensive.

---

## Strong Comment: Async Race

**Issue**: The search effect updates results for every completed request without checking whether the response still matches the latest query.

**Reasoning**: A slow response for an older query can overwrite newer results when the user types quickly. That makes the UI unreliable under normal network variance.

**Suggested improvement**: Reuse the repository request hook if it already handles cancellation. Otherwise cancel the previous fetch with `AbortController` or ignore stale responses.

---

## Strong Comment: Component Boundary

**Issue**: `UserRow` decides whether the current viewer can suspend the user and also renders row layout.

**Reasoning**: Authorization policy changes are likely to affect other user actions, while row layout changes are local to the table. Keeping the policy inside the row makes future permission changes harder to audit.

**Suggested improvement**: Move the permission decision to an existing authorization helper or pass a narrow `canSuspend` prop from the owner that already knows viewer permissions.

---

## Strong Comment: Stale Closure

**Issue**: `handleSubmit` captures `formData` in a `useCallback` with an empty dependency array. If `formData` changes after mount, the handler always submits the original values.

**Reasoning**: The user can fill in new data, see it in the UI, and submit — but the request will contain the values from the first render. This bug is invisible in fast tests and only surfaces in flows where `formData` changes after the form is first displayed (pre-population from an API, URL param pre-fill, etc.).

**Suggested improvement**: Add `formData` to the `useCallback` dependency array. If the handler must remain stable (e.g., passed to a memoized child that reads it via ref), use a ref that is updated in a `useLayoutEffect` to always reflect the current value without recreating the callback.

```tsx
// Bug
const handleSubmit = useCallback(() => {
  submitForm(formData); // formData is frozen at first render
}, []);

// Fix 1: correct deps
const handleSubmit = useCallback(() => {
  submitForm(formData);
}, [formData]);

// Fix 2: stable callback via ref, for memoized children
const formDataRef = useRef(formData);
useLayoutEffect(() => { formDataRef.current = formData; });
const handleSubmit = useCallback(() => {
  submitForm(formDataRef.current);
}, []);
```

---

## Strong Comment: Missing Cleanup

**Issue**: The effect subscribes to `channel` but does not unsubscribe on cleanup. If the component unmounts or `channel` changes, the old subscription remains active.

**Reasoning**: The handler will be called after unmount and will attempt to update state on a dead component. In React 18 StrictMode, the effect runs twice on mount, creating a second subscription that is never removed. Over a user session with navigation, subscriptions accumulate and may cause duplicate events, memory leaks, or stale state updates.

**Suggested improvement**: Return a cleanup function that unsubscribes.

```tsx
// Bug
useEffect(() => {
  channel.subscribe(handleMessage);
}, [channel]);

// Fix
useEffect(() => {
  channel.subscribe(handleMessage);
  return () => channel.unsubscribe(handleMessage);
}, [channel]);
```

---

## Strong Comment: Impossible State

**Issue**: `isLoading`, `isError`, and `isSuccess` are independent booleans. The component can render with `isLoading=true` and `isError=true` simultaneously, which has no defined behavior.

**Reasoning**: When the user triggers a retry, the handler sets `isLoading=true` without clearing `isError`. The UI can show a spinner and an error message at the same time. This is an impossible state that the type system permits but the application cannot meaningfully render. Future changes that add a fourth state (e.g., `isStale`) will require auditing every combination of these flags.

**Suggested improvement**: Replace the three booleans with a discriminated union. Impossible combinations become unrepresentable.

```tsx
// Bug
const [isLoading, setIsLoading] = useState(false);
const [isError, setIsError] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);

// Fix
type Status = 'idle' | 'loading' | 'error' | 'success';
const [status, setStatus] = useState<Status>('idle');
```

---

## Strong Comment: Server Action Missing Authorization

**Issue**: `deleteComment` verifies the user is authenticated but does not verify they own the comment being deleted. Any authenticated user can delete any comment by supplying a different `commentId`.

**Reasoning**: This is an Insecure Direct Object Reference (IDOR). The user ID is checked (authn) but resource ownership is not (authz). An attacker who knows or guesses a comment ID can delete content they do not own. This is a High finding because it is exploitable with a direct API call and no client-side workaround prevents it.

**Suggested improvement**: Fetch the comment before deleting and compare `comment.authorId` to `session.user.id`. Return a Forbidden error if they do not match.

```tsx
// Bug
async function deleteComment(commentId: string) {
  'use server';
  const session = await getServerSession();
  if (!session) throw new Error('Unauthorized');
  await db.comments.delete({ where: { id: commentId } });
}

// Fix
async function deleteComment(commentId: string) {
  'use server';
  const session = await getServerSession();
  if (!session) throw new Error('Unauthorized');

  const comment = await db.comments.findUnique({ where: { id: commentId } });
  if (!comment || comment.authorId !== session.user.id) {
    throw new Error('Forbidden');
  }

  await db.comments.delete({ where: { id: commentId } });
}
```

---

## Weak Comment: Line Count

> This component is too long. Split it into smaller components.

This is weak because it does not identify which responsibility should move or what future risk the split prevents.

---

## Weak Comment: Memoization

> Wrap this handler in `useCallback` for performance.

This is weak unless a memoized child or measured performance issue depends on stable identity.

---

## Weak Comment: Preference

> I prefer early returns here.

This is weak unless the current branching hides states or creates maintenance risk.
