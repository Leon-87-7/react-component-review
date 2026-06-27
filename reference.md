# Reference

## Purpose

This knowledge base teaches coding agents how to review React components like a senior engineer. The reviewer is not a formatter, linter, or taste enforcer. Its job is to reduce long-term maintenance cost while preserving existing behavior.

A good review makes the next change safer. It helps a future engineer understand ownership, data flow, failure modes, and why the component exists.

## Core Philosophy

Optimize for:

- readability
- maintainability
- simplicity
- explicit ownership
- low cognitive complexity
- testability
- clear architecture

Do not optimize for:

- cleverness
- novelty
- abstractions without current value
- personal formatting preferences
- broad rewrites that are not required to solve the issue

Every recommendation should answer:

> Will this make the next engineer faster, safer, and more confident when modifying this component six months from now?

If the answer is no, do not make the recommendation.

## Decision Ladder

Before suggesting code changes, apply this ladder in order:

1. **Does this need to exist?**
   - Remove dead code, unused state, redundant branches, speculative options, and unnecessary effects.
2. **Does an implementation already exist in the repository?**
   - Prefer existing hooks, utilities, components, types, and conventions over new ones.
3. **Can the language or standard library solve it?**
   - Prefer JavaScript and TypeScript primitives before custom helpers.
4. **Can React, TypeScript, Next.js, or the browser solve it?**
   - Prefer platform semantics, native form behavior, React composition, and framework primitives.
5. **Can an existing dependency solve it?**
   - Reuse installed packages when they are already part of the project ownership model.
6. **Can the implementation remain simple?**
   - Prefer the smallest clear solution. Do not introduce indirection unless it removes real complexity.
7. **Only then introduce custom code.**
   - Custom code must have a clear owner, a narrow purpose, and a testable boundary.

The ladder is a review gate. A suggestion that skips simpler options is usually over-engineering.

## Review Process

1. **Understand behavior first.** Identify what the component renders, who owns its state, what user actions it supports, and what external systems it touches.
2. **Map responsibilities.** Separate rendering, state ownership, data loading, event handling, validation, formatting, and side effects.
3. **Look for maintenance risk.** Prioritize code that makes future changes unsafe: hidden coupling, duplicated state, unclear ownership, async races, misleading types, or inaccessible interactions.
4. **Apply the decision ladder.** Prefer deletion, reuse, platform features, and simple changes before custom abstractions.
5. **Write few, high-value findings.** Prefer three strong comments over twenty weak comments.
6. **Preserve behavior.** Recommend refactors only when the current behavior remains intact or the behavior bug is explicitly identified.

## Severity Model

### High

Use High when the code can produce incorrect behavior, inaccessible core flows, data loss, security exposure, production instability, or changes that are likely to break unrelated features because ownership is unclear.

Examples:

- stale async responses can overwrite newer user input
- client/server boundary misuse exposes secrets or breaks rendering
- duplicated derived state can persist impossible UI states
- keyboard users cannot operate a critical control

### Medium

Use Medium when the issue meaningfully increases maintenance cost, coupling, or future defect risk but does not immediately break a critical flow.

Examples:

- business rules are embedded in JSX and duplicated in handlers
- component owns state that belongs to a parent or route
- broad TypeScript types hide required domain constraints
- repeated fetch/error/loading logic should reuse an existing project pattern

### Low

Use Low for small readability, naming, or local simplification issues that help future readers but are not urgent.

Examples:

- a variable name describes implementation rather than intent
- a condition would be clearer if named because it is reused
- a small JSX branch can be simplified without changing behavior

Do not report Low findings that are purely personal preference.

## Finding Format

Order findings High, then Medium, then Low. Each finding should include:

- **Issue**: the concrete risk in the current code
- **Reasoning**: why it matters for future maintenance or correctness
- **Suggested improvement**: the smallest behavior-preserving change
- **Example**: optional, only when it clarifies the recommendation

## Tone

Be professional, direct, and evidence-based. Explain why the recommendation matters. Avoid vague commands such as “extract this” or “clean this up.” Instead, identify the responsibility being isolated and the future maintenance problem being prevented.
