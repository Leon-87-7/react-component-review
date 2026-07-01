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

### Effect as lifecycle is an anti-pattern

Treating `useEffect` as `componentDidMount` or `componentDidUpdate` is the most common misuse. Effects that set state in response to other state changes almost always indicate a derived state problem or a missing event handler.

```tsx
// Anti-pattern: effect coordinating internal state
useEffect(() => {
  if (selectedItem) {
    setDetails(null);
    setIsLoading(true);
  }
}, [selectedItem]);
// Fix: reset state inside the event handler that changes selectedItem,
// or use the key prop to force a controlled remount.
```

### React 18 and StrictMode

In StrictMode, React intentionally runs setup and cleanup twice in development to expose missing cleanups. Any effect that breaks or duplicates behavior under double-invocation has a missing cleanup function, not a StrictMode problem.

```tsx
// This will behave differently in dev vs prod without cleanup
useEffect(() => {
  const sub = eventBus.subscribe(handler);
  // Missing: return () => sub.unsubscribe();
}, []);
```

Flag any effect that registers a subscription, listener, or resource without a cleanup return.

### Concurrent rendering

React 18 can interrupt and replay renders. State updates inside `startTransition` are interruptible. Side effects tied to concurrent state updates can fire multiple times. Code that assumes rendering is synchronous and single-pass is fragile under concurrent features.

## Stale Closures

A stale closure is the most common subtle bug in React. It occurs when a callback or effect captures a value from render scope but does not include that value in its dependency array, or does not re-close over the current value.

```tsx
// Bug: query is captured once at mount, never updated
useEffect(() => {
  fetchResults(query).then(data => setResults(data));
}, []); // query must be in deps

// Bug: formData is stale on every submit after first change
const handleSubmit = useCallback(() => {
  submitForm(formData);
}, []); // formData must be in deps, or restructure to read from ref
```

A strong finding identifies the value that goes stale and the user action that makes the bug observable (e.g., "if the user changes the query while a request is in flight, the next submit will use the old value").

The escape hatch for values you need to read without re-creating the callback is a ref:

```tsx
const formDataRef = useRef(formData);
useLayoutEffect(() => { formDataRef.current = formData; });
const handleSubmit = useCallback(() => {
  submitForm(formDataRef.current); // always current, callback stays stable
}, []);
```

Flag stale closures whenever you see a non-empty value from render scope absent from a dependency array.

## Key Prop

The `key` prop controls whether React reuses or remounts a component. There are two distinct uses and both have failure modes:

**Key as identity** (most common): key must be stable and unique across the list lifetime. Index keys fail when items are inserted, removed, sorted, or filtered — React preserves the wrong local state (controlled input values, focus, animation state) for the wrong item.

**Key as reset** (intentional technique): changing a key forces a controlled remount, resetting all internal state. This is valid and often better than synchronizing state with a `useEffect`. Flag it only if the reset is accidental or if `key={null}` or `key={undefined}` could occur.

```tsx
// Fails on sort or filter — row state follows position, not item
items.map((item, i) => <Row key={i} {...item} />)

// Fails if name is not stable across the session
items.map(item => <Row key={item.name} {...item} />)

// Valid reset pattern — clears form when userId changes
<ProfileForm key={userId} userId={userId} />
```

When you see index keys, confirm whether the list is ever sorted, filtered, or reordered before escalating.

## Memoization

Do not suggest `useMemo` or `useCallback` without evidence. Memoization adds cognitive overhead and dependency maintenance. It is useful when it prevents known expensive recalculation, stabilizes identity for a memoized child, or prevents repeated setup of an external integration.

Avoid comments like "wrap this in `useCallback`." Instead explain the measured or structural reason identity stability matters.

**Inline object and array literals in props are a structural problem, not a memoization opportunity.** A new object reference on every render will re-trigger any `useEffect` in a child that lists the object as a dependency, and will defeat `React.memo` on that child. This is a correctness risk, not a performance preference.

```tsx
// New config object every render — defeats memo, re-triggers child effects
<DataGrid config={{ pageSize: 10, sortable: true }} onSelect={item => setSelected(item)} />
// Fix: define config outside the component or memoize it; define handler with useCallback
// only if DataGrid is memoized and stable identity is required.
```

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
