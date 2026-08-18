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

## General Rules

- Keep event registrations lightweight — register handlers, don't execute logic
- If initialization is order-dependent, use `Bootstrapper.Initialized` with explicit `EventHandler` priorities
- Never put blocking I/O (HTTP calls, file operations) in startup events without async offloading
