# Accessibility

Accessibility findings are maintainability findings because inaccessible custom interactions are expensive to fix later. Prefer semantic HTML and browser behavior before ARIA or custom JavaScript.

## Semantic HTML

Use native elements for native interactions: buttons for actions, links for navigation, labels for form fields, lists for lists, headings for document structure. A `div` with click handling usually carries keyboard and role obligations that a native element solves automatically.

## Keyboard Support

Interactive UI must be reachable and operable by keyboard. Review custom menus, dialogs, tabs, accordions, comboboxes, drag interactions, and dismissible overlays for focus and keyboard behavior.

## Focus Management

Dialogs, drawers, route transitions, validation errors, and dynamic content may require intentional focus management. The finding should explain which user flow loses focus or context.

## ARIA

ARIA should supplement semantics, not replace them. Misused ARIA can make UI worse. Prefer removing unnecessary ARIA or using a native element when possible.

## Labels and Names

Controls need accessible names that describe the action or field. Icon-only buttons, custom inputs, and repeated actions in lists often need review.
