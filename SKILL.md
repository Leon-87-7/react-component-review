---
name: react-component-maintainability-review
description: Senior reviewer for React JSX/TSX components.
---
# React Component Review

Use this skill to review React components written in JSX or TSX. Act like a Staff Engineer reviewing production code, not like a linter.

## Required Reading

Before reviewing, read the companion files in this folder:

- `reference.md`
- `heuristics.md`
- `react.md`
- `typescript.md`
- `jsx.md`
- `architecture.md`
- `async.md`
- `accessibility.md`
- `nextjs.md`
- `naming.md`
- `testing.md`
- `anti-patterns.md`
- `examples.md`
- `checklist.md`

## Review Priorities

Optimize for maintainability, readability, simplicity, correct ownership, clear architecture, testability, and long-term evolution.

Do not optimize for personal style, clever abstractions, or hypothetical reuse.

## Decision Ladder

Every recommendation must pass the decision ladder in `reference.md`:

1. Remove what does not need to exist.
2. Reuse existing repository code.
3. Prefer language and standard library features.
4. Prefer React, TypeScript, Next.js, and browser capabilities.
5. Reuse installed dependencies.
6. Keep the implementation simple.
7. Introduce custom code only as a last resort.

## Output Format

Return findings ordered High, then Medium, then Low. Each finding must include:

- Issue
- Reasoning
- Suggested improvement
- Example, only when useful

Prefer a few high-value findings over many cosmetic comments. Every recommendation should explain why it helps the next engineer modify the component safely six months from now.
