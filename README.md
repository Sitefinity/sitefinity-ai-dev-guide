# Sitefinity AI Development Guide

Curated instruction files for GitHub Copilot that enforce good practices, performance patterns, and architectural discipline in Sitefinity projects.

## How to Use

### Option 1: Copy into your project

Copy the `.github/instructions/` folder into your project's root. The `sitefinity-*.instructions.md` files will be automatically picked up by GitHub Copilot.

### Option 2: Clone and pick what you need

```bash
git clone https://github.com/Sitefinity/sitefinity-ai-dev-guide.git
```

Copy individual files from `.github/instructions/` into your project's `.github/instructions/` folder.

### Option 3: Git submodule

```bash
git submodule add https://github.com/Sitefinity/sitefinity-ai-dev-guide.git .sitefinity-guide
```

Then copy the instruction files into your `.github/instructions/` directory.

## What's Included

| File | Purpose |
|------|---------|
| `sitefinity-ai-behavior` | Teaches Copilot to understand context before acting, check for ripple effects |
| `sitefinity-data-layer` | Manager patterns, transactions, unit-of-work lifecycle |
| `sitefinity-querying` | Server-side filtering, no premature materialization, projections |
| `sitefinity-caching` | When and how to cache, invalidation, expensive operations |
| `sitefinity-bootstrapper` | What belongs in Global.asax / Bootstrapper events |
| `sitefinity-performance` | General performance mindset, N+1 avoidance, batch operations |
| `sitefinity-code-quality` | DRY, separation of concerns, reuse patterns |

## Will These Conflict with My Own Instructions?

No. All files use a `sitefinity-` prefix. Your own project-specific instructions (e.g., `my-project-conventions.instructions.md`) will coexist without conflicts. The default Copilot file `.github/copilot-instructions.md` is also unaffected.

## Requirements

- VS Code or Visual Studio with GitHub Copilot extension
- Files must be placed in `.github/instructions/` at your repository root