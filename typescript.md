# TypeScript

Review TypeScript for whether it communicates the component's contract. Strong types should make invalid states difficult to represent and future changes safer.

## Props

Props are an ownership boundary. A prop type should explain what the component needs, not expose whatever shape a parent happens to have.

Look for:

- inline prop types that are duplicated across components
- props named after implementation details rather than user intent
- broad optional props that allow impossible combinations
- callbacks with unclear responsibility, such as `onChange` carrying several meanings

Prefer discriminated unions when variants have different required props.

## Avoid `any`

`any` removes reviewable contracts. A finding is strongest when `any` hides a real risk: unvalidated API data, event payload confusion, or assumptions about nullable fields.

Do not mechanically demand perfect typing for every migration file. Recommend the narrowest useful type that protects the component boundary.

## Broad Types

Types like `object`, `Record<string, unknown>`, `Function`, `ReactNode` everywhere, or overly optional domain objects can hide required behavior. Ask what the component actually reads and model that.

## Generics

Generics are useful when the component preserves a relationship between inputs and outputs. They are harmful when they make a local component harder to read without increasing safety.

Flag unnecessary generics when a concrete type would better communicate the domain.

## Unions

Unions should make states clearer. Confusing unions often appear when several optional booleans represent modes. Prefer a discriminated union with a `status`, `kind`, or `type` field when it prevents invalid combinations.

## API Data

Do not trust remote data just because it has a TypeScript type. Review whether parsing, validation, nullability, and fallback ownership are clear. UI components should not silently assume backend invariants unless the repository has a shared validated client.
