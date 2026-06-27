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

## TypeScript

- Do prop types express the component contract?
- Are invalid states difficult to represent?
- Is `any` hiding a real boundary risk?
- Are generics and unions clarifying rather than obscuring behavior?

## Accessibility

- Are native elements used where possible?
- Can keyboard users operate interactive controls?
- Are labels, names, focus, and ARIA handled intentionally?
