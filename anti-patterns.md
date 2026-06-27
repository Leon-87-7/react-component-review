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

“Best practice” is not reasoning. Explain the specific maintenance, correctness, accessibility, or ownership risk in the code under review.
