# React Component Review

A senior-engineer review knowledge base for React components written in JSX or TSX.

This repository is designed for use with coding agents such as Codex and Claude Code. It teaches the agent how to review React components for maintainability, architecture, readability, accessibility, TypeScript quality, and long-term ownership.

The goal is not to make code match a personal style. The goal is to reduce future maintenance cost while preserving behavior.

## Core idea

Every recommendation should pass the decision ladder:

1. Does this need to exist? If not, remove it. YAGNI.
2. Does this already exist in the codebase? Reuse it, do not rewrite it.
3. Can the standard library solve it? Use it.
4. Can React, TypeScript, Next.js, or the browser platform solve it? Use native capabilities.
5. Can an installed dependency solve it? Use it before writing custom code.
6. Can it stay one clear line? Keep it one clear line.
7. Only then write the minimum custom implementation that works.

## Files

- `SKILL.md` - entry point for Codex-style skill usage.
- `reference.md` - philosophy and review process.
- `heuristics.md` - smells, severity, and review signals.
- `examples.md` - good and weak review comments.
- `checklist.md` - final review pass.
- `react.md` - hooks, effects, state, composition, and performance.
- `typescript.md` - TSX-specific review guidance.
- `jsx.md` - JSX readability and render structure.
- `architecture.md` - component boundaries and ownership.
- `async.md` - async UI/networking patterns.
- `naming.md` - naming heuristics.
- `testing.md` - testability considerations.
- `accessibility.md` - accessibility review.
- `nextjs.md` - Next.js App Router guidance.
- `anti-patterns.md` - common bad recommendations and false positives.

## Recommended usage

Point your coding agent at this repository or copy the folder into your agent skills directory. Ask it to review a React component using `SKILL.md` as the entry point and to consult all companion Markdown files before producing findings.

## Review output

Findings should be ordered by severity: High, Medium, Low.

Each finding should include:

- Issue
- Reasoning
- Suggested improvement
- Example, when useful

Prefer a few high-value comments over many cosmetic comments.
