# React

Review React code for ownership, data flow, and side-effect boundaries. Do not reward hook usage for its own sake. The best React component is often the one with fewer effects, fewer state variables, and clearer composition.

## State Ownership

State should be owned by the component that understands the reason it changes. Push state down when only a child uses it. Lift state up when siblings need the same source of truth. Move state to a route, context, or external store only when that scope reflects real ownership.

Review risks:

- parent components storing transient UI details for many children
- child components mutating copied props
- multiple booleans representing one state machine
- state reset behavior depending on component remounts rather than explicit transitions

Prefer a single domain-shaped state when it prevents invalid combinations.

## Derived State

Do not store values that can be derived from props or state during render. Derived state adds synchronization work and creates stale UI risk.

Good review wording:

> `selectedLabel` appears to duplicate `selectedId` and `options`. If either changes independently, the UI can show a stale label. Deriving the label during render keeps one source of truth and removes the synchronization effect.

## Effects

`useEffect` should synchronize React with an external system: network, DOM API, subscription, timer, storage, analytics, or imperative third-party library. It should not be the default tool for deriving values or chaining internal state updates.

Question every effect:

- What external system is being synchronized?
- Are all dependencies represented honestly?
- Can this be done during render or inside the event handler instead?
- Does cleanup cover subscriptions, timers, and pending async work?
- Can stale responses update state after newer input?

## Memoization

Do not suggest `useMemo` or `useCallback` without evidence. Memoization adds cognitive overhead and dependency maintenance. It is useful when it prevents known expensive recalculation, stabilizes identity for a memoized child, or prevents repeated setup of an external integration.

Avoid comments like “wrap this in `useCallback`.” Instead explain the measured or structural reason identity stability matters.

## Component Boundaries

Split components when the split isolates a responsibility with an independent reason to change. Do not split only because a file is long.

Good boundaries often separate:

- data loading from presentation
- form state from field rendering
- domain decisions from layout
- repeated accessible interaction patterns from page-specific content

Bad boundaries create prop tunnels, generic names, or components that cannot be understood without their parent.

## Composition

Prefer composition when it makes ownership explicit. Avoid prop drilling through components that do not use the props; this makes intermediate components harder to change. However, do not introduce context for a single nearby consumer.

## Event Handlers

Handlers should be easy to scan for user intent. If a handler validates, transforms, persists, logs, navigates, and updates UI state, identify which responsibility deserves a named function or existing service.

The goal is not smaller functions. The goal is to make failure ownership and future changes obvious.
