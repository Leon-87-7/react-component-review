# Naming

Names should reduce the amount of code a future engineer must read. Review naming when it hides ownership, intent, or domain meaning. Do not comment on naming as personal taste.

## Intent Over Implementation

Prefer names that describe why a value exists rather than how it is implemented.

Examples:

- `canSubmit` is clearer than `isButtonEnabled` when the value represents form validity and permissions.
- `visibleOrders` is clearer than `filteredData` when the list has domain meaning.
- `handleInviteAccepted` is clearer than `onClick` when the handler coordinates a workflow.

## Boolean Names

Boolean names should read naturally and reveal the condition being modeled. Avoid negative booleans when they create double negatives in JSX.

## Callback Names

Callbacks should reveal responsibility. `onChange` is fine for simple controlled inputs, but domain events often need names like `onAddressSelected`, `onInviteRevoked`, or `onBillingContactChanged`.

## Generic Names

Names like `data`, `item`, `value`, `utils`, and `manager` are acceptable in tiny scopes but risky at component boundaries. Ask whether the name forces readers to inspect implementation before understanding the contract.
