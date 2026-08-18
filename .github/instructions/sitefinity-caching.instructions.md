---
applyTo: "**/*.cs"
---

# Sitefinity Caching

## When to Cache

- Data that is expensive to compute or slow to retrieve (external APIs, complex aggregations)
- Data that is read frequently but changes rarely (navigation structures, settings, taxonomies)
- Results of queries that hit the database on every request with the same parameters

## When Not to Cache

- User-specific data that varies per request (unless keyed by user)
- Data that changes on every request or is always unique
- Small lookups that are already fast — caching adds complexity without benefit

## How to Cache in Sitefinity

- Use `ICacheManager` from `Telerik.Sitefinity.Services` for application-level caching
- Use Sitefinity's output cache and cache dependencies for page/widget output
- Set appropriate expiration — no cache should live forever without invalidation logic
- Use cache keys that include all varying parameters (culture, site, filters)

## Invalidation

- Subscribe to Sitefinity's data events to invalidate when the underlying data changes
- Prefer short TTLs over complex invalidation logic when the data source is unpredictable

## Patterns

```csharp
ICacheManager cache = SystemManager.GetCacheManager(CacheManagerInstance.Global);
string cacheKey = $"Products_{culture}_{categoryId}";

if (!cache.Contains(cacheKey))
{
    var data = GetExpensiveData(culture, categoryId);
    cache.Add(cacheKey, data, CacheItemPriority.Normal, null,
        new AbsoluteTime(TimeSpan.FromMinutes(10)));
}

return cache.GetData(cacheKey);
```

## Avoid

- Caching inside loops — compute once, cache once
- Cache keys that don't account for culture or multisite context
- Caching mutable objects that callers might modify (cache a copy or immutable data)
