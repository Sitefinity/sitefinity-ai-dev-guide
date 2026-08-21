# Sitefinity AI Development Guide

Curated instruction files for AI coding agents that enforce good practices, performance patterns, and architectural discipline in Sitefinity projects.

Works with GitHub Copilot, Cursor, Claude Code, Codex, OpenCode, and Windsurf.

## Quick Setup

Paste this into your agent's chat from inside your Sitefinity project:

```
Fetch and execute the appropriate instructions to set me up for Sitefinity development from https://raw.githubusercontent.com/Sitefinity/sitefinity-ai-dev-guide/main/agent-setup.md
```

The agent detects which tool it is, then writes the instruction files to the right place in the right format. Re-run the same prompt any time to update to the latest versions.

## Where the Files Land

| Agent | Location |
|-------|----------|
| GitHub Copilot (VS Code) | `.github/instructions/*.instructions.md` |
| Cursor | `.cursor/rules/*.mdc` |
| Windsurf | `.windsurf/rules/*.md` |
| Claude Code | `.sitefinity/instructions/` + imports in `CLAUDE.md` |
| Codex, OpenCode | inlined into `AGENTS.md` |

The guidance is identical in every case — only the frontmatter and file location differ, because each tool reads rules from a different place.

Two differences worth knowing:

- **Claude Code, Codex, and OpenCode have no per-glob rule scoping.** Copilot, Cursor, and Windsurf only load a rule when you touch a matching file; the other three keep all of it in context all the time. The `Applies to:` line in those formats is guidance for the model, not an enforced filter.
- **Windsurf caps workspace rules** at roughly 12,000 characters. The current set is close to that ceiling, so the setup installs as many files as fit and tells you which it skipped rather than letting Windsurf drop them silently.

## Manual Setup

<details>
<summary>Prefer to do it yourself?</summary>

### Option 1: Copy into your project

Copy the `.github/instructions/` folder into your project's root. The `sitefinity-*.instructions.md` files are picked up automatically by GitHub Copilot. For other agents, see the table above for the target location and convert the frontmatter accordingly.

### Option 2: Clone and pick what you need

```bash
git clone https://github.com/Sitefinity/sitefinity-ai-dev-guide.git
```

Copy individual files from `.github/instructions/` into your project.

### Option 3: Git submodule

```bash
git submodule add https://github.com/Sitefinity/sitefinity-ai-dev-guide.git .sitefinity-guide
```

Then copy the instruction files into your agent's rules directory.

</details>

## What's Included

| File | Purpose |
|------|---------|
| `sitefinity-ai-behavior` | Understand context before acting, check for ripple effects |
| `sitefinity-data-layer` | Manager patterns, transactions, unit-of-work lifecycle |
| `sitefinity-querying` | Server-side filtering, no premature materialization, projections |
| `sitefinity-caching` | When and how to cache, invalidation, expensive operations |
| `sitefinity-bootstrapper` | What belongs in Global.asax / Bootstrapper events |
| `sitefinity-performance` | General performance mindset, N+1 avoidance, batch operations |
| `sitefinity-code-quality` | DRY, separation of concerns, reuse patterns |

## Will These Conflict with My Own Rules?

No. Every file uses a `sitefinity-` prefix, so your own rules coexist untouched. The setup never modifies `.github/copilot-instructions.md`, `.cursorrules`, or `.windsurfrules`.

For `AGENTS.md` and `CLAUDE.md`, which are single shared files, the setup writes only inside a marked block:

```markdown
<!-- sitefinity-ai-dev-guide:start -->
...
<!-- sitefinity-ai-dev-guide:end -->
```

Everything outside those markers is left alone, and re-running the setup replaces only the block.

## Requirements

- Any of: GitHub Copilot (VS Code), Cursor, Claude Code, Codex, OpenCode, Windsurf

Visual Studio support depends on your version recognising `.github/instructions/*.instructions.md`. If yours does not, copy the guidance into `.github/copilot-instructions.md` instead.
- Files must be placed in `.github/instructions/` at your repository root