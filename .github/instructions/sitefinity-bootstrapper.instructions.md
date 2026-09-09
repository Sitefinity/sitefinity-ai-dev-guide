---
applyTo: "**/Global.asax*"
---

# Sitefinity Bootstrapper and Global.asax Events

## Bootstrapper_Initialized / Bootstrapped

Use for:
- Registering event handlers (content events, workflow events, page events)
- Registering custom virtual paths or route extensions
- Subscribing to system events that need to be active for the application lifetime

Do not use for:
- Heavy data processing or migrations — these block application startup
- Operations that depend on specific content existing (it may not be imported yet)
- Long-running tasks — offload to a scheduled task or background job instead

## Application_Start

Use for:
- Route registration
- Dependency injection container setup
- Global filters

Do not use for:
- Sitefinity-specific API calls — the CMS is not yet initialized at this point
- Anything that requires the database or content modules to be ready

## Application_BeginRequest / EndRequest

Use sparingly for:
- Cross-cutting concerns that genuinely apply to every request (custom headers, timing)

Do not use for:
- Business logic — this runs on every single request including static files and resources
- Data operations — this is too early/late in the pipeline for content work

## Subscribing to data events

Subscribe through `EventHub` from inside `Bootstrapper.Bootstrapped`, not directly in `Application_Start` — the CMS is not ready yet at that point.

```csharp
protected void Application_Start(object sender, EventArgs e)
{
    Bootstrapper.Bootstrapped += this.Bootstrapper_Bootstrapped;
}

private void Bootstrapper_Bootstrapped(object sender, EventArgs e)
{
    EventHub.Subscribe<IDataEvent>(evt => this.DataEventHandler(evt));
}
```

If you do not want to modify `Global.asax`, put the subscription in a separate class and wire it up with `PreApplicationStartMethodAttribute` in `AssemblyInfo.cs`. Both approaches are equivalent — pick the one that matches how the project is already organised.

### Writing the handler

`IDataEvent` fires on create, update, and delete for **every** content type in the system. A handler that is slow, or that hits the database unnecessarily, adds that cost to every content operation an editor performs.

- Filter on `ItemType` first and return early. Do this before loading anything.
- The event carries only `Action`, `ItemType`, `ItemId` and `ProviderName`. Resolving the actual item is a database round-trip, so only do it once you know you care about the item. Once you have filtered to a known type, use that type's manager — `NewsManager.GetManager(providerName)`. Reach for `ManagerBase.GetMappedManager(itemType, providerName)` only when the type is not known until runtime.
- Subscribe to a specific event contract instead of `IDataEvent` when one exists — it is narrower and fires far less often.
- Keep handlers free of blocking I/O. Offload external calls and long work to a scheduled task.
- Do not assume ordering between handlers, and do not let an exception escape — a failing handler affects the content operation that raised it.

```csharp
public void DataEventHandler(IDataEvent eventInfo)
{
    if (eventInfo.ItemType != typeof(NewsItem))
        return;

    var manager = NewsManager.GetManager(eventInfo.ProviderName);
    var item = manager.GetNewsItem(eventInfo.ItemId);
}
```

## General Rules

- Keep event registrations lightweight — register handlers, don't execute logic
- If initialization is order-dependent, use `Bootstrapper.Initialized` with explicit `EventHandler` priorities
- Never put blocking I/O (HTTP calls, file operations) in startup events without async offloading
