# Accessibility

Accessibility findings are maintainability findings because inaccessible custom interactions are expensive to fix later. Prefer semantic HTML and browser behavior before ARIA or custom JavaScript.

## Semantic HTML

Use native elements for native interactions: buttons for actions, links for navigation, labels for form fields, lists for lists, headings for document structure. A `div` with click handling usually carries keyboard and role obligations that a native element solves automatically.

## Keyboard Support

Interactive UI must be reachable and operable by keyboard. Review custom menus, dialogs, tabs, accordions, comboboxes, drag interactions, and dismissible overlays for focus and keyboard behavior.

## Focus Management

Focus management is not a generic concern — each trigger has a specific required behavior. Flag the missing case by name.

| Trigger | Required focus behavior |
|---|---|
| Modal / dialog opens | Focus moves into the dialog (first focusable element or the dialog itself via `tabIndex={-1}`) |
| Modal / dialog closes | Focus returns to the element that triggered the open |
| Route change (SPA) | Focus moves to the new page heading, a skip-nav target, or a live region announces the page title |
| Form submit with errors | Focus moves to the error summary or the first invalid field |
| Inline error appears | Field's `aria-describedby` points to the error, or focus moves if the field loses its value |
| Expandable section opens (accordion, disclosure) | Focus stays on the trigger; content follows in DOM order |
| Toast / notification appears | A `role="status"` or `role="alert"` live region announces the message; focus does not move |
| Drawer opens | Focus moves to the drawer; background is inert |
| Dropdown menu opens | Focus moves to the first menu item |
| Dropdown menu closes | Focus returns to the trigger button |

When writing a finding about focus management, name the trigger and the missing behavior rather than saying "check focus management." Example:

> When the confirmation dialog closes, focus is not returned to the Delete button that opened it. The user's focus position is lost, making keyboard navigation unpredictable. Call `triggerRef.current?.focus()` in the `onClose` handler.

Implementation pattern for modal focus:

```tsx
const dialogRef = useRef<HTMLDivElement>(null);

useEffect(() => {
  if (isOpen) {
    dialogRef.current?.focus();
  }
}, [isOpen]);

// On close, return focus to trigger:
const triggerRef = useRef<HTMLButtonElement>(null);
const handleClose = () => {
  setIsOpen(false);
  triggerRef.current?.focus();
};
```

## ARIA

ARIA should supplement semantics, not replace them. Misused ARIA can make UI worse. Prefer removing unnecessary ARIA or using a native element when possible.

Common misuse patterns to flag:

- `role="button"` on a `<div>` — use `<button>` instead
- `aria-label` on a non-interactive element — usually unnecessary
- `aria-hidden="true"` on an element that is still focusable — traps keyboard users in invisible content
- `aria-live` regions that are not in the DOM on initial render — the region must exist before content is injected for screen readers to announce it

## Labels and Names

Controls need accessible names that describe the action or field. Icon-only buttons, custom inputs, and repeated actions in lists often need review.

```tsx
// Missing accessible name — screen reader announces "button"
<button onClick={onDelete}><TrashIcon /></button>

// Fix options:
<button onClick={onDelete} aria-label="Delete comment">
  <TrashIcon aria-hidden="true" />
</button>

// Or visually hidden text:
<button onClick={onDelete}>
  <TrashIcon aria-hidden="true" />
  <span className="sr-only">Delete comment</span>
</button>
```

Repeated actions in lists need context: "Delete" repeated ten times is not meaningful. The accessible name should include the item: "Delete Invoice #1042."
