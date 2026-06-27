# Architecture

Architecture review asks whether responsibilities are placed where future maintainers expect them. Good architecture reduces the number of files and concepts required to make a safe change.

## Cohesion

A component is cohesive when its code changes for one reason. Mixed responsibilities are a review target when UI layout, data fetching, permissions, formatting, analytics, and persistence all change together.

Suggest extracting or moving only the responsibility that has a different owner or change cadence.

## Coupling

Coupling is not always bad. Components must depend on something. Review problematic coupling: hidden dependencies, duplicated assumptions, or knowledge of distant implementation details.

Examples:

- a presentational component knows route params and API field names
- a form field knows how the entire page submits
- a child component depends on parent state shape rather than a narrow prop contract

## Abstractions

Abstractions are justified when they remove current duplication, clarify ownership, or isolate volatility. They are not justified because code might be reused someday.

Before recommending an abstraction, name the responsibility it owns and the code it allows future changes to avoid touching.

## Reuse

Prefer repository-local patterns over new inventions. If a component already has a shared button, data hook, form helper, or error boundary pattern, recommend reuse when it preserves consistency and reduces maintenance.

Do not recommend reuse when the shared abstraction does not fit the current behavior.
