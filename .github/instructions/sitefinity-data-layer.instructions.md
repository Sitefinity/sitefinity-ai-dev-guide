---
applyTo: "**/*.cs"
---

# Sitefinity Data Layer

## Managers

- Use the specific typed manager when the content type is known (e.g., `NewsManager.GetManager()`, `PageManager.GetManager()`, `DynamicModuleManager.GetManager()`)
- Fall back to `ManagerBase.GetMappedManager(type)` only when the type is resolved at runtime
- Managers are tied to a transaction scope; do not store them in static fields or long-lived variables
- Always work within the manager's intended lifecycle — get it, use it, let it go

## Transactions

- Use `FluentSitefinity` or the native API transaction support rather than manual `OpenAccessContext` manipulation
- Commit transactions explicitly when performing write operations
- Keep transactions short — do your reads and validation first, then open the write scope
- Never nest unrelated operations inside the same transaction

## Working with Content Items

- Use the typed content API (`DynamicModuleManager`) for dynamic content
- Prefer `GetDataItems(type)` with server-side filtering over loading all items
- When creating or modifying items, always call `SaveChanges()` or `Commit()` — don't rely on implicit saves
- Use `Lifecycle.Publish()` explicitly when items need to go live — don't assume auto-publish

## Avoid

- Storing manager references in fields that outlive the request
- Wrapping data layer calls in unnecessary try-catch that swallows errors
- Opening multiple manager instances for the same type in the same operation
