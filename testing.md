# Testing

Review testability as a design signal. Code that is hard to test is often mixing responsibilities or hiding dependencies.

## What to Look For

- business rules that can only be tested through a large rendered component
- async behavior without clear loading, success, and failure states
- components that import global state, routing, analytics, and network clients directly
- handlers that combine validation, persistence, navigation, and presentation
- inaccessible UI that cannot be queried by role or label

## Good Recommendations

Recommend moving pure business decisions to named functions only when it improves clarity or allows focused tests. Prefer testing user-visible behavior for rendering and interaction.

## Avoid

- demanding tests for trivial JSX branches
- snapshot tests as a substitute for behavioral assertions
- extracting code solely to test private implementation details

A useful testing comment explains what future regression the test would catch.
