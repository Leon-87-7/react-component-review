# Checklist

Use this final pass before returning review findings.

## Behavior

- Have I preserved existing behavior unless reporting a behavior bug?
- Did I identify user-visible flows affected by the issue?
- Did I avoid broad rewrites when a smaller change works?

## Decision Ladder

- Can the code be deleted instead of refactored?
- Does the repository already have a component, hook, type, or helper for this?
- Can JavaScript, TypeScript, React, Next.js, or the browser solve this directly?
- Is an installed dependency already responsible for this problem?
- Is custom code truly necessary?

## Finding Quality

- Is each finding tied to concrete code?
- Does each finding explain why the issue matters?
- Is the suggested improvement smaller than the risk it addresses?
- Are findings ordered High, Medium, Low?
- Did I avoid cosmetic comments?

## React

- Is state owned by the right component?
- Is derived state avoided unless justified?
- Are effects synchronizing with external systems rather than internal state?
- Are async effects cleaned up or guarded against stale updates?
- Is memoization recommended only with evidence?
- Does every `useEffect` that reads a value from render scope include that value in its dependency array, or restructure to avoid the stale read?
- Does every `useEffect` that opens a subscription, timer, listener, or WebSocket have a cleanup return function?
- Are inline object and array literals in JSX props causing referential instability for memoized children or effect dependencies?
- Are `key` props stable identifiers (not index) when the list can be sorted, filtered, or reordered?
- Is the `key` prop being used intentionally as a reset mechanism, or accidentally causing unexpected remounts?

## TypeScript

- Do prop types express the component contract?
- Are invalid states difficult to represent?
- Is `any` hiding a real boundary risk?
- Are generics and unions clarifying rather than obscuring behavior?
- Are multiple boolean flags modeling a single lifecycle? If booleans cannot co-exist, is a discriminated union more appropriate?
- Is API data being cast with `as SomeType` without runtime validation? Does the project have a schema library that should be used at the boundary?

## Async and Data

- Does every async event handler disable the trigger (button, form) during the inflight request to prevent double-submit?
- Are race conditions possible between changing inputs and in-flight requests?
- Does the repository already have a data-fetching hook that should be reused here?

## Accessibility

- Are native elements used where possible?
- Can keyboard users operate all interactive controls?
- Are labels, names, and ARIA handled intentionally?
- When a modal or dialog opens, does focus move into it?
- When a modal or dialog closes, does focus return to the trigger?
- When a form is submitted with errors, does focus move to the error summary or first invalid field?
- Do icon-only buttons have accessible names (aria-label or visually-hidden text)?
- Do repeated list actions include the item context in their accessible name?

## Next.js

- Is `use client` present only where client-only capabilities are required?
- Does every Server Action that mutates data verify both authentication AND per-resource authorization?
- Are `process.env` variables without `NEXT_PUBLIC_` prefix used only in server code?
- Does every `<Suspense>` boundary have a co-located or ancestor `<ErrorBoundary>`?
