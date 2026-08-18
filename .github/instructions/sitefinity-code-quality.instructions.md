---
applyTo: "**/*.cs"
---

# Sitefinity Code Quality

## Don't Repeat Yourself

- If the same logic exists in more than one place, extract it into a shared method or service
- Before writing new code, check if a utility or helper already exists in the project
- When fixing a bug, search for the same pattern elsewhere — it likely has the same issue

## Separation of Concerns

- Keep data access out of presentation layers — use a service or repository layer
- Controllers and widgets should orchestrate, not contain business logic
- Keep view models thin — they hold data for display, not behavior

## Reuse Over Rewrite

- If another component does something similar to what you need, extend or compose — don't duplicate
- Use base classes or shared services for common operations (resolving URLs, getting current site, formatting dates)
- Configuration values should come from one place — don't hardcode the same setting in multiple files

## Method Design

- A method should do one thing — if you're naming it `GetAndProcessAndSave`, split it
- Keep methods short enough to understand without scrolling
- Return early for guard clauses instead of deep nesting

## Dependencies

- Favor constructor injection over service location when the project supports it
- Don't resolve the same service multiple times in one class — store it in a field
- Avoid static helpers that hide dependencies and make testing harder

## When Modifying Existing Code

- Match the existing code style in that file — don't introduce a new convention mid-file
- If the existing pattern is poor but works, don't refactor it as part of an unrelated change
- Make the smallest change that solves the problem correctly
