# Heuristics

Use these heuristics to decide whether a React component deserves a review finding. They are signals, not automatic rules. A finding is useful only when it identifies a real maintenance or correctness risk.

## Strong Signals

### Unclear ownership

Ask who owns each value and side effect. State should live with the component that can answer why the value changes. If multiple components can mutate the same concept indirectly, future changes become risky.

Look for:

- child components copying props into local state without a reset strategy
- route-level concerns hidden inside leaf components
- parent components coordinating too many unrelated child details
- shared state represented by parallel booleans instead of a single domain state

### Duplicated derived state

Derived state often creates impossible UI states because the source value and derived copy can diverge. Prefer deriving during render unless there is a proven performance or interaction reason to store it.

Common examples:

- `filteredItems` stored in state when it can be calculated from `items` and `query`
- `isEmpty`, `hasError`, or `isComplete` stored separately from the data that determines it
- props mirrored into state without handling prop changes

### Business logic inside JSX

JSX should communicate structure. When authorization rules, pricing rules, validation rules, or workflow transitions are buried in render expressions, future engineers must mentally execute the template to understand behavior.

Recommend naming the decision or moving it to an existing domain helper when that clarifies ownership.

### Oversized handlers

Event handlers should coordinate user intent, not contain an entire workflow. Long handlers are risky when they mix validation, analytics, network calls, state transitions, and navigation.

Prefer isolating the responsibility that changes independently. Do not extract simply because the handler is long.

### Hidden side effects

Side effects are dangerous when they are triggered by render, by derived calculations, or by hooks with unclear dependencies. Effects should synchronize with external systems, not compensate for avoidable state modeling.

### High coupling

A component is highly coupled when small UI changes require understanding remote data shape, permissions, routing, analytics, and formatting at the same time. Recommend clearer boundaries when they reduce the number of concepts a future change must touch.

## Weak Signals

These are not findings by themselves:

- a file is long
- JSX nesting exists
- a callback is inline
- `useMemo` is absent
- a component could theoretically be reused
- a helper could be made generic
- another pattern is popular

Turn a weak signal into a finding only when you can explain the concrete maintenance risk.

## False Positive Filter

Before writing a finding, ask:

1. Is behavior currently wrong, fragile, or hard to change?
2. Can I point to specific code causing that risk?
3. Is the suggested change smaller or simpler than the current risk?
4. Am I avoiding a purely stylistic preference?
5. Would I still leave this comment if review bandwidth allowed only three comments?

If any answer is no, skip or downgrade the finding.
