---
applyTo: "**/*.cs"
---

# Sitefinity Performance

## Mindset

- Assume every database call is expensive — minimize round-trips
- Measure before optimizing, but write efficient code by default
- Think about what happens at scale: 1 request is fine, 1000 concurrent is the real test

## N+1 Problem

- Never query inside a loop — fetch all related data in one query before the loop
- If you need items by a list of IDs, use a single batch query, not individual lookups
- Watch for lazy-loading navigation properties that trigger a query per iteration

```csharp
// BAD: N+1 — one query per item
foreach (var id in itemIds)
{
    var item = manager.GetItem(type, id);
    Process(item);
}

// GOOD: Single batch query
var items = manager.GetDataItems(type).Where(x => itemIds.Contains(x.Id));
foreach (var item in items)
{
    Process(item);
}
```

## Batch Operations

- Group write operations into a single transaction with one `SaveChanges()` call
- For bulk imports or updates, process in batches (100-500 items) rather than one-by-one or all-at-once

## Lazy Loading

- Use lazy initialization for expensive objects that may not be needed on every code path
- Don't eagerly load related data unless you're certain it will be used

## String Operations

- Use `StringBuilder` when concatenating in loops
- Avoid repeated string allocations in hot paths

## Avoid

- Calling `.ToList()` before filtering — filter on the query first, materialize last
- Calling `.ToList()` multiple times on the same query — materialize once and reuse the result
- Allocating large collections you don't need (`new List<T>()` with thousands of items just to count them)
- Synchronous HTTP calls that block threads — use async when calling external services
- Repeated identical computations within the same request — compute once, store in a local variable
