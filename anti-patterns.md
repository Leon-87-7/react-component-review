# Anti-patterns

These are recommendations the reviewer should avoid unless there is concrete evidence they solve a current problem.

## Premature Memoization

Do not suggest `useMemo` or `useCallback` merely because values or handlers are recreated. Memoization has its own maintenance cost and can hide dependency bugs.

## Extraction by Line Count

Do not split a component only because it is long. Split when the new boundary isolates a responsibility, clarifies ownership, or removes meaningful duplication.

## Custom Hooks After One Use

A custom hook is valuable when it names a reusable stateful behavior or isolates an effectful integration. A hook created after one usage can add indirection without reducing complexity.

## Generic Utilities Just in Case

Avoid recommending generic utilities for hypothetical future reuse. Prefer local, concrete code until duplication or volatility justifies generalization.

## Replacing Working Code Because a Pattern Exists

Do not suggest a different library, state manager, or framework feature solely because it is popular. The suggested pattern must reduce current risk while preserving behavior.

## Cosmetic Review Noise

Avoid comments about personal formatting, import order, minor naming taste, or alternative syntax unless they materially affect understanding. Automated tools should handle style.

## Over-broad Best Practices

"Best practice" is not reasoning. Explain the specific maintenance, correctness, accessibility, or ownership risk in the code under review.

## TypeScript as Documentation

Using a TypeScript type assertion (`as SomeType`) on external data provides false safety. The type system is erased at runtime. A cast does not validate that the data matches the type — it only suppresses the compiler's ability to warn when it does not.

Do not recommend "just add a type" for API responses. Recommend the project's runtime validation approach. Flag `as X` applied to `JSON.parse`, `fetch().then(r => r.json())`, or external storage as a meaningful gap, not a minor style issue.

## God Context

Placing unrelated state in a single context because it is "global" causes every consumer to re-render when any part of the value changes. The cost is invisible until the app grows.

Signs: a single context value contains authentication, UI preferences, request state, feature flags, and notifications. Each slice has a different update frequency. Consumers that only need `user.name` re-render on every background data poll.

Recommendation: split context by update frequency. Stable config (theme, locale) in one context; high-frequency state (request status, notifications) in another.

## `useEffect` as Lifecycle

Treating `useEffect` as `componentDidMount` / `componentDidUpdate` / `componentWillUnmount` is the most common conceptual error in React. Effects exist to synchronize with external systems. When they are used to coordinate internal React state, they are almost always compensating for a missing event handler or a derived state problem.

```tsx
// Anti-pattern: effect coordinating internal state
useEffect(() => {
  if (selectedItem) {
    setDetailPanel(null);
    setIsLoading(true);
    setError(null);
  }
}, [selectedItem]);
// What this is actually doing: resetting state when an event occurs.
// The fix: reset state in the event handler that sets selectedItem.
// Or: use key={selectedItem.id} on the detail panel to force a remount.
```

Before flagging a "missing useEffect," ask whether the code could be moved into the event handler that triggered the change. Usually it can.

## Context for Nearby State

Reaching for context because prop drilling is inconvenient is different from reaching for context because the state is genuinely cross-cutting. Context adds indirection, makes data flow harder to trace, and makes components harder to test and reuse.

Before recommending context, verify that the state is consumed by components in genuinely different subtrees. If the consuming components are close in the tree, prop drilling or component composition is almost always the right answer.

## Inline Object and Array Literals in Props

Inline object and array literals create a new reference on every render. This is distinct from a memoization preference — it is a correctness concern when:

- A child component has `React.memo` wrapping that depends on prop identity
- A `useEffect` in the child lists the prop as a dependency — the effect will re-run on every parent render even if the values are semantically identical

```tsx
// Defeats memo and re-triggers child effects on every render
<DataGrid config={{ pageSize: 10, sortable: true }} columns={['id', 'name']} />

// Fix: define stable values outside the component or memoize them
const GRID_CONFIG = { pageSize: 10, sortable: true } as const;
const GRID_COLUMNS = ['id', 'name'] as const;
<DataGrid config={GRID_CONFIG} columns={GRID_COLUMNS} />
```

Flag this when the receiving component is memoized or when the prop is listed in a `useEffect` dependency array in the child. Do not flag it as a general memoization concern.

## Suspense Without ErrorBoundary

A component that suspends (via `use`, a Suspense-compatible data library, or a lazy import) without a co-located ErrorBoundary will propagate errors to the nearest boundary, which may be far up the tree. In Next.js App Router, an unhandled Server Component error produces a full-page error screen.

Every `<Suspense>` boundary should have a sibling `<ErrorBoundary>` unless an ancestor boundary is explicitly documented as covering that subtree.

```tsx
// Risky: thrown errors propagate to page-level boundary
<Suspense fallback={<Spinner />}>
  <UserProfile userId={id} />
</Suspense>

// Correct: errors are caught at the feature boundary
<ErrorBoundary fallback={<ProfileError />}>
  <Suspense fallback={<Spinner />}>
    <UserProfile userId={id} />
  </Suspense>
</ErrorBoundary>
```
