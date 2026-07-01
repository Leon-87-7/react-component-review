# Next.js

Review Next.js components for correct ownership across server and client boundaries. Prefer server components and framework primitives when they reduce shipped JavaScript and clarify data ownership.

## Server and Client Boundaries

Do not mark a component with `use client` unless it needs client-only capabilities such as state, effects, event handlers, browser APIs, or client context. Unnecessary client components increase bundle size and can force children into the client tree.

## Data Fetching

Prefer the repository's App Router data-fetching conventions. Server-side data ownership can simplify loading states, remove client effects, and avoid duplicated fetch logic.

## Caching

Cache behavior should be intentional. Review whether data should be static, revalidated, dynamic, or user-specific. Misplaced caching can show stale or unauthorized data.

## Server Actions

Server Actions should have clear validation, authorization, error handling, and user feedback. UI components should not assume success without modeling pending and failure states.

### Authorization model

Every Server Action that mutates data must satisfy three requirements, in order:

1. **Authentication** — verify the user is logged in
2. **Authorization** — verify the user has permission for the specific resource being mutated (not just that they are logged in)
3. **Input validation** — validate and sanitize all inputs before touching the database

Skipping step 2 is the most common error. A logged-in user should not be able to mutate another user's data by changing an ID in the request.

```tsx
// Missing authorization — any logged-in user can delete any post
async function deletePost(postId: string) {
  'use server';
  const session = await getServerSession();
  if (!session) throw new Error('Unauthorized');
  // Bug: no ownership check — attacker supplies a different postId
  await db.posts.delete({ where: { id: postId } });
}

// Correct: authenticate, authorize per-resource, then mutate
async function deletePost(postId: string) {
  'use server';
  const session = await getServerSession();
  if (!session) throw new Error('Unauthorized');

  const post = await db.posts.findUnique({ where: { id: postId } });
  if (!post || post.authorId !== session.user.id) {
    throw new Error('Forbidden');
  }

  await db.posts.delete({ where: { id: postId } });
}
```

Flag missing ownership checks as High when the action mutates or reads private user data. The attack vector is simple: any authenticated user who can call the action supplies a different resource ID and operates on data they do not own.

### Pending and failure states

Server Actions invoked from forms should model at least three states in the UI: idle, pending (use `useFormStatus` or a local `isPending` flag), and error. Actions that navigate on success but show nothing on failure leave users with no feedback and may allow duplicate submissions.

## Secrets and Environment

Client components must not access secrets or server-only modules. If a component crosses the boundary accidentally, the finding should be High when it risks exposure or runtime failure.

Environment variables prefixed with `NEXT_PUBLIC_` are shipped to the browser. Variables without that prefix are server-only. Review any `process.env` access in a client component.
