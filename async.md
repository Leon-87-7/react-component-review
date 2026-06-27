# Async

Async review focuses on correctness over style. Network and timing bugs often appear only under slow connections, rapid input, retries, or navigation.

## Race Conditions

Look for requests triggered by changing props, search input, filters, or route params. A slower old request can overwrite a newer result unless cancellation or stale-response guards exist.

A strong finding explains the user action that creates the race.

## Cancellation and Cleanup

Effects that start async work should define what happens when inputs change or the component unmounts. Use the repository's existing request/cancellation pattern when available. Otherwise prefer platform primitives such as `AbortController` for fetch.

## Loading and Error Ownership

Loading and error states should be owned by the boundary that can decide what the user sees. Avoid scattering `isLoading`, `error`, retries, and toast behavior across unrelated children.

## Duplicate Fetch Logic

If the repository already has a data hook or framework loader for the resource, prefer reuse. Duplicated fetch logic creates inconsistent caching, error handling, authorization, and retry behavior.

## Async Handlers

Async event handlers should make failure behavior explicit. Review whether disabled states, duplicate submissions, optimistic updates, rollback, and navigation after success are handled intentionally.
