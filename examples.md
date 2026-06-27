# Examples

Use examples to calibrate review tone. Strong comments identify a concrete issue, explain why it matters, and suggest the smallest useful improvement.

## Strong Comment: Derived State

**Issue**: `filteredUsers` is stored separately from `users` and `query`.

**Reasoning**: This creates two sources of truth. If `users` refreshes while `query` stays the same, the rendered list can become stale unless every update path remembers to recalculate `filteredUsers`.

**Suggested improvement**: Derive `filteredUsers` during render from `users` and `query`, or memoize only if profiling shows the filter is expensive.

## Strong Comment: Async Race

**Issue**: The search effect updates results for every completed request without checking whether the response still matches the latest query.

**Reasoning**: A slow response for an older query can overwrite newer results when the user types quickly. That makes the UI unreliable under normal network variance.

**Suggested improvement**: Reuse the repository request hook if it already handles cancellation. Otherwise cancel the previous fetch with `AbortController` or ignore stale responses.

## Strong Comment: Component Boundary

**Issue**: `UserRow` decides whether the current viewer can suspend the user and also renders row layout.

**Reasoning**: Authorization policy changes are likely to affect other user actions, while row layout changes are local to the table. Keeping the policy inside the row makes future permission changes harder to audit.

**Suggested improvement**: Move the permission decision to an existing authorization helper or pass a narrow `canSuspend` prop from the owner that already knows viewer permissions.

## Weak Comment: Line Count

> This component is too long. Split it into smaller components.

This is weak because it does not identify which responsibility should move or what future risk the split prevents.

## Weak Comment: Memoization

> Wrap this handler in `useCallback` for performance.

This is weak unless a memoized child or measured performance issue depends on stable identity.

## Weak Comment: Preference

> I prefer early returns here.

This is weak unless the current branching hides states or creates maintenance risk.
