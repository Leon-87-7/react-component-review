# Reference

## Core Philosophy

Reduce future maintenance cost while preserving behavior.

Before recommending any implementation, always apply this decision ladder:

1. Does this need to exist? → If not, recommend removing it (YAGNI).
2. Already in this codebase? → Reuse it, don't rewrite.
3. Stdlib does it? → Use it.
4. Native platform feature? → Use it.
5. Installed dependency? → Use it.
6. One line? → Prefer the simplest readable solution.
7. Only then: implement the minimum custom solution that works.

Optimize for simplicity, readability, cohesion, and long-term ownership.

Never recommend abstractions solely because they seem cleaner. Every abstraction should reduce duplication, clarify intent, or isolate responsibility.
