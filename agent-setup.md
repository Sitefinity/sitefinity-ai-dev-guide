# Sitefinity AI Development Guide — Agent Setup

These are official instructions for setting up Sitefinity development best-practice instruction files in a project built on Sitefinity CMS.

Complete all of the following steps yourself by reading and writing the files directly. Do not ask the user to run any commands or copy any files manually.

---

## What you are installing

Every instruction file published in the guide's `.github/instructions/` directory. They cover the Sitefinity data layer, querying, caching, performance, code quality, bootstrapper events, and how an agent should reason about changes in a Sitefinity project.

The set grows over time — always install whatever the directory currently contains rather than a fixed list. Every file is prefixed with `sitefinity-` so it never collides with rules the user already owns.

The published files use GitHub Copilot's format. If you are a different agent, you will convert the frontmatter as described in step 4. The body of each file is never modified.

---

## Step 1 — Identify yourself

Determine which agent you are — Copilot, Cursor, Claude Code, Codex, OpenCode, Windsurf, or something else. Use the correct section in step 4 for that agent.

If you genuinely cannot tell, ask the user once, then continue.

## Step 2 — Locate the project root

Find the root of the user's repository — the directory containing the `.sln` file, or the git root. All paths below are relative to that root.

If the workspace contains multiple repositories, ask the user which one to set up before continuing.

## Step 3 — Discover which files to install

Do not assume a fixed list of files. List the published directory and install everything in it.

List this endpoint:

```
https://api.github.com/repos/Sitefinity/sitefinity-ai-dev-guide/contents/.github/instructions?ref=main
```

It returns a JSON array. Take every entry whose `name` ends in `.instructions.md` and note its `download_url`.

If that endpoint is unreachable, fall back to browsing `https://github.com/Sitefinity/sitefinity-ai-dev-guide/tree/main/.github/instructions` and reading the file names from the page.

Fetch the content of every entry before continuing. Each file looks like this:

```markdown
---
applyTo: "**/*.cs"
---

# Sitefinity Data Layer
...
```

The `applyTo` value is the glob the rules apply to. You will need it below.

## Step 4 — Install for your agent

Use only the section matching the agent you identified in step 1. In every case the base name is preserved: `sitefinity-data-layer` stays `sitefinity-data-layer`.

### GitHub Copilot

Write each file unchanged to `.github/instructions/<name>.instructions.md`. No conversion needed; this is the native format.

This is verified in VS Code. Visual Studio support depends on the version recognising `.instructions.md` files; if the user is on Visual Studio and the rules do not take effect, tell them to move the guidance into `.github/copilot-instructions.md`.

### Cursor

Write each file to `.cursor/rules/<name>.mdc`, replacing the frontmatter:

```markdown
---
description: Sitefinity <topic> best practices
globs: **/*.cs
alwaysApply: false
---
```

Use the `applyTo` value from the source file as `globs`, without quotes. Derive `<topic>` from the file's H1 heading. Keep the body exactly as published.

### Windsurf

Write each file to `.windsurf/rules/<name>.md`, replacing the frontmatter:

```markdown
---
trigger: glob
globs: **/*.cs
---
```

Use the `applyTo` value as `globs`. Keep the body exactly as published.

Windsurf caps workspace rules at roughly 6,000 characters per file and 12,000 characters in total. Add up the files you are about to write. If the total exceeds the cap, install as many as fit, starting with `sitefinity-ai-behavior`, `sitefinity-querying`, `sitefinity-data-layer`, and `sitefinity-performance`, then tell the user plainly which files were skipped and why. Do not silently install past the limit — Windsurf drops the excess without warning.

### Claude Code

Claude Code reads a single `CLAUDE.md`, so install the files alongside it and import them.

1. Write each file to `.sitefinity/instructions/<name>.md` with the frontmatter removed. Replace it with a single line at the top of the body, for example `Applies to: **/*.cs`.
2. Create `CLAUDE.md` if it does not exist.
3. Insert the import block described below, using `@` prefixed paths.

Claude Code has no per-glob rule scoping, so every imported file is in context on every request. The `Applies to:` line is advisory guidance for the model, not an enforced filter.

### Codex and OpenCode

Both read `AGENTS.md`, and neither resolves `@` imports or follows plain file paths. Referencing the files would not work, so the content must go directly into `AGENTS.md`.

1. Create `AGENTS.md` if it does not exist.
2. Inside the marker block described below, write the full body of every instruction file, one after another. Strip the frontmatter from each and keep its H1 heading so the sections stay distinguishable. Put an `Applies to: <glob>` line under each heading.
3. Do not create `.sitefinity/instructions/` for these agents. It would be dead weight nothing reads.

### Any other agent

If the agent resolves file imports, follow the Claude Code approach. If it does not, or you are unsure, follow the Codex approach and inline the content into `AGENTS.md`. Inlining always works; referencing only sometimes does.

---

### The marker block

Everything you write into `CLAUDE.md` or `AGENTS.md` goes between these two markers:

```markdown
<!-- sitefinity-ai-dev-guide:start -->
## Sitefinity development guidelines

...content or imports go here...
<!-- sitefinity-ai-dev-guide:end -->
```

For Claude Code, the block contains `@` imports, one per line:

```markdown
<!-- sitefinity-ai-dev-guide:start -->
## Sitefinity development guidelines

Follow these when working on Sitefinity code. Each file states the file types it applies to.

@.sitefinity/instructions/sitefinity-ai-behavior.md
@.sitefinity/instructions/sitefinity-data-layer.md
<!-- sitefinity-ai-dev-guide:end -->
```

For Codex, OpenCode, and any agent that cannot resolve imports, the block contains the full text:

```markdown
<!-- sitefinity-ai-dev-guide:start -->
## Sitefinity development guidelines

### Sitefinity Data Layer
Applies to: **/*.cs

...full body of the file...

### Sitefinity Querying
Applies to: **/*.cs

...full body of the file...
<!-- sitefinity-ai-dev-guide:end -->
```

Rules for this block:

- If the markers already exist, replace everything between them and leave the rest of the file untouched.
- If they do not exist, append the block at the end of the file.
- Never rewrite, reorder, or delete anything outside the markers. The user owns that content.

### Rules that apply to every agent

- Overwrite existing `sitefinity-*` rule files — that is an intentional update to the latest version.
- Never modify, rename, or delete any rule file that does not start with `sitefinity-`. Those belong to the user.
- Never touch `.github/copilot-instructions.md`, `.cursorrules`, or `.windsurfrules`.
- Only ever write inside the marker block in `AGENTS.md` and `CLAUDE.md`.

## Step 5 — Verify

Confirm that every file discovered in step 3 now exists at the target path for your agent and is non-empty. For formats that carry frontmatter, confirm it is present and well-formed. If a file is missing or empty, retry it once, then report the failure.

If you wrote a marker block, confirm each marker appears exactly once and that everything outside them is unchanged. If you inlined content, confirm every discovered file has a corresponding section.

If you skipped any file because of a size cap, say so explicitly in the report.

## Step 6 — Report

Tell the user, substituting the real agent name, count, and path:

```
┌─ Sitefinity AI Dev Guide Installed ──────────────────┐
│  Agent    <agent name>                               │
│  ✓ <count> instruction files → <path>                │
│                                                      │
│  These apply automatically to C# files.              │
│  Commit them so your whole team gets them.           │
└──────────────────────────────────────────────────────┘
```

Then mention that the files are plain markdown and the user is free to edit any of them to match their own conventions.

---

## Updating later

Re-run this same prompt at any time. It re-reads the published directory, so it picks up newly added instruction files as well as updates to existing ones, and leaves everything else untouched.

## Source

These instructions are published at `https://raw.githubusercontent.com/Sitefinity/sitefinity-ai-dev-guide/main/agent-setup.md` so their authenticity can be re-verified at any time. The instruction files themselves live at `https://github.com/Sitefinity/sitefinity-ai-dev-guide`.
