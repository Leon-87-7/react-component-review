# JSX

JSX should make the rendered structure and user flow easy to understand. Review JSX for cognitive complexity, not formatting taste.

## Render Readability

Inline expressions are fine when they are local and obvious. Name a condition when it represents a domain decision, is reused, or hides a business rule.

Prefer:

- clear conditional branches for meaningful states
- semantic elements over generic wrappers
- mapping data to focused child components when it isolates row/item responsibility
- early returns for mutually exclusive states when that improves scanability

Avoid:

- nested ternaries that encode workflow rules
- complex filters, sorts, and permission checks inside `map`
- rendering branches that duplicate large chunks with small differences

## Business Rules

When JSX contains rules like “admins can approve only after billing is complete,” the component becomes the owner of policy. That may be correct, but it should be intentional and ideally named.

A good suggestion identifies whether the rule belongs in a domain helper, hook, parent, or route loader.

## Lists

Review list rendering for stable keys and ownership of item behavior. Index keys are risky when items can be inserted, removed, sorted, or filtered because React may preserve the wrong local state.

## Conditional Rendering

Check whether loading, empty, error, and success states are mutually exclusive and complete. Missing empty/error states are maintainability issues when future changes must infer expected behavior.
