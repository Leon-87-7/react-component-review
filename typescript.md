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

## Impossible State Detection

The most impactful TypeScript review signal is multiple boolean flags that model a single lifecycle. When booleans can be independently true, the type system permits combinations that the application cannot meaningfully render.

```tsx
// This permits isLoading=true and isError=true simultaneously
type RequestState = {
  isLoading: boolean;
  isError: boolean;
  isSuccess: boolean;
};

// Correct: impossible combinations are unrepresentable
type RequestState =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'error'; error: Error }
  | { status: 'success'; data: ResponseData };
```

Detection heuristic: any time you see two or more boolean flags that begin with `is` or `has` on the same type, ask whether they can logically be true at the same time. If not, that is a discriminated union that has not been written yet. Flag this as Medium when it creates render ambiguity, or High when it permits a state that produces incorrect UI (e.g., showing a loading spinner and an error message simultaneously).

## Avoid `any`

`any` removes reviewable contracts. A finding is strongest when `any` hides a real risk: unvalidated API data, event payload confusion, or assumptions about nullable fields.

Do not mechanically demand perfect typing for every migration file. Recommend the narrowest useful type that protects the component boundary.

## Runtime Validation vs TypeScript Cast

TypeScript types do not exist at runtime. A type assertion (`as User`) applied to an API response is not validation — it is documentation that can be wrong. When the API changes and the component does not update, the type system silently lies.

```tsx
// This provides false safety — the cast proves nothing at runtime
const data = await fetch('/api/user').then(r => r.json()) as User;
// data.name could be undefined, null, or a number

// What to recommend when the project already uses a schema library
const result = UserSchema.safeParse(await fetch('/api/user').then(r => r.json()));
if (!result.success) { /* handle parse failure */ }
const data = result.data; // actually User, guaranteed at runtime
```

Flag `as SomeType` applied to `fetch().then(r => r.json())` or any external data source as Medium. Recommend the project's existing validation approach (zod, io-ts, valibot, etc.) or note that the team should establish one if none exists.

## Broad Types

Types like `object`, `Record<string, unknown>`, `Function`, `ReactNode` everywhere, or overly optional domain objects can hide required behavior. Ask what the component actually reads and model that.

## Generics

Generics are useful when the component preserves a relationship between inputs and outputs. They are harmful when they make a local component harder to read without increasing safety.

Flag unnecessary generics when a concrete type would better communicate the domain.

## Unions

Unions should make states clearer. Confusing unions often appear when several optional booleans represent modes. Prefer a discriminated union with a `status`, `kind`, or `type` field when it prevents invalid combinations.

## API Data

Do not trust remote data just because it has a TypeScript type. Review whether parsing, validation, nullability, and fallback ownership are clear. UI components should not silently assume backend invariants unless the repository has a shared validated client.
