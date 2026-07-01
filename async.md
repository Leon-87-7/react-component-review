# Async

Async review focuses on correctness over style. Network and timing bugs often appear only under slow connections, rapid input, retries, or navigation.

## Race Conditions

Look for requests triggered by changing props, search input, filters, or route params. A slower old request can overwrite a newer result unless cancellation or stale-response guards exist.

A strong finding explains the user action that creates the race: "if the user types quickly enough to dispatch two requests before the first resolves, the slower response can overwrite the newer one."

## Cancellation and Cleanup

Effects that start async work should define what happens when inputs change or the component unmounts. Use the repository's existing request/cancellation pattern when available. Otherwise prefer platform primitives such as `AbortController` for fetch.

```tsx
useEffect(() => {
  const controller = new AbortController();
  fetchResults(query, { signal: controller.signal })
    .then(data => setResults(data))
    .catch(err => { if (err.name !== 'AbortError') setError(err); });
  return () => controller.abort();
}, [query]);
```

## Subscriptions and External Resources

`fetch` is only one category of async resource. Every effect that opens an external connection or registers a listener must clean it up. This is one of the most common production bugs in React codebases.

Flag any effect that starts one of the following without a cleanup return:

**`addEventListener`**
```tsx
useEffect(() => {
  window.addEventListener('resize', handleResize);
  return () => window.removeEventListener('resize', handleResize);
}, [handleResize]);
```

**`setInterval` / `setTimeout`**
```tsx
useEffect(() => {
  const id = setInterval(tick, 1000);
  return () => clearInterval(id);
}, [tick]);
```

**WebSocket**
```tsx
useEffect(() => {
  const ws = new WebSocket(url);
  ws.onmessage = handler;
  return () => ws.close();
}, [url]);
```

**Third-party subscriptions (EventEmitter, RxJS, Pusher, etc.)**
```tsx
useEffect(() => {
  const sub = channel.subscribe(handler);
  return () => sub.unsubscribe();
}, [channel]);
```

**MutationObserver / IntersectionObserver / ResizeObserver**
```tsx
useEffect(() => {
  const observer = new IntersectionObserver(callback, options);
  observer.observe(ref.current);
  return () => observer.disconnect();
}, []);
```

Missing cleanup causes timers to fire after unmount, event handlers to accumulate on each mount, and WebSocket messages to call `setState` on a dead component tree. These bugs are silent in tests and only appear under navigation or hot reload.

In StrictMode (React 18 default in dev), effects run twice. A missing cleanup makes the double-invocation visible. Treat StrictMode failures as real bugs, not framework quirks.

## Loading and Error Ownership

Loading and error states should be owned by the boundary that can decide what the user sees. Avoid scattering `isLoading`, `error`, retries, and toast behavior across unrelated children.

## Duplicate Fetch Logic

If the repository already has a data hook or framework loader for the resource, prefer reuse. Duplicated fetch logic creates inconsistent caching, error handling, authorization, and retry behavior.

## Async Handlers

Async event handlers should make failure behavior explicit. Review whether disabled states, duplicate submissions, optimistic updates, rollback, and navigation after success are handled intentionally.

A common missed case: if `handleSubmit` is async and the button is not disabled during the inflight request, the user can trigger duplicate submissions. Flag this when the handler performs a mutation.
