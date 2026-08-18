---
applyTo: "**/*.cs"
---

# AI Behavior for Sitefinity Projects

Before making any change, follow this process:

## Understand First, Then Act

- Read the surrounding code to understand the architectural concept — not just the single method
- Identify which Sitefinity features and APIs are being used (Content API, Dynamic modules, Ecommerce, Forms, Security, etc.)
- Check if similar functionality already exists in the project — reuse it instead of writing a duplicate
- Consider whether the code you're writing belongs in the current location, or should be extracted into a shared/common layer for reuse

## Check for Ripple Effects

- Before modifying a method, search for all callers and usages across the project
- Before changing a model or DTO, check which views, controllers, and services consume it
- Before altering event handlers or subscribers, verify what other parts of the project depend on the same event
- If a base class or interface is involved, check all implementations in the project

## Don't Invent When a Pattern Exists

- If the project already has a way of doing something (caching, logging, resolving URLs), use that — don't create a new approach
- If other widgets/controllers in the project follow a structure, match it
- Look for shared utilities or helpers in the project before writing inline logic
- If you're writing something that could be reused by other parts of the project, place it in a common/shared location rather than burying it in a specific widget or controller

## Ask When Uncertain

- If the impact of a change is unclear, flag it rather than guessing
- If you find conflicting patterns in the codebase, highlight the inconsistency
