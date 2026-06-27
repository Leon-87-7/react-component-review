# Next.js

Review Next.js components for correct ownership across server and client boundaries. Prefer server components and framework primitives when they reduce shipped JavaScript and clarify data ownership.

## Server and Client Boundaries

Do not mark a component with `use client` unless it needs client-only capabilities such as state, effects, event handlers, browser APIs, or client context. Unnecessary client components increase bundle size and can force children into the client tree.

## Data Fetching

Prefer the repository's App Router data-fetching conventions. Server-side data ownership can simplify loading states, remove client effects, and avoid duplicated fetch logic.

## Caching

Cache behavior should be intentional. Review whether data should be static, revalidated, dynamic, or user-specific. Misplaced caching can show stale or unauthorized data.

## Server Actions

Server actions should have clear validation, authorization, error handling, and user feedback. UI components should not assume success without modeling pending and failure states.

## Secrets and Environment

Client components must not access secrets or server-only modules. If a component crosses the boundary accidentally, the finding should be High when it risks exposure or runtime failure.
