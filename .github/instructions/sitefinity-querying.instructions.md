---
applyTo: "**/*.cs"
---

# Sitefinity Querying

## Filter Before Retrieving

- Apply `.Where()` clauses before materializing results — filtering happens on the database, not in memory
- Never call `.ToList()` or `.ToArray()` and then filter the collection afterward
- Use `.Take()` and `.Skip()` for paging at the query level

## Do Not Materialize Prematurely

- Keep queries as `IQueryable<T>` as long as possible
- Call `.ToList()` only at the final consumption point — not in intermediate helper methods
- If a method returns data for further filtering, return `IQueryable<T>`, not `List<T>`

## Projections

- Select only the fields you need when the full object is not required
- Use `.Select()` to project into lightweight DTOs instead of loading entire content items
- For display-only scenarios, avoid loading navigation properties you won't use

## Paging and Limits

- Always apply a limit — never retrieve unbounded result sets
- Use `.Take(count)` at the query level, not after materialization
- For large datasets, implement proper paging with `.Skip()` and `.Take()`

## Common Mistakes

```csharp
// BAD: Loads all items into memory, then filters
var items = manager.GetDataItems(type).ToList().Where(x => x.Status == ContentLifecycleStatus.Live);

// GOOD: Filters on the database
var items = manager.GetDataItems(type).Where(x => x.Status == ContentLifecycleStatus.Live);

// BAD: Retrieves everything when you need 10
var items = manager.GetDataItems(type).ToList().Take(10);

// GOOD: Database returns only 10 rows
var items = manager.GetDataItems(type).Where(x => x.Visible).Take(10);
```
