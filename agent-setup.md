# Sitefinity AI Development Guide — Agent Setup

These are official instructions for setting up Sitefinity development best-practice instruction files in a project built on Sitefinity CMS.

Complete all of the following steps yourself by reading and writing the files directly. Do not ask the user to run any commands or copy any files manually.

---

## What you are installing

Every Copilot instruction file published in the guide's `.github/instructions/` directory. They cover the Sitefinity data layer, querying, caching, performance, code quality, bootstrapper events, and how an agent should reason about changes in a Sitefinity project.

The set grows over time — always install whatever the directory currently contains rather than a fixed list. Every file is prefixed with `sitefinity-` so it never collides with instruction files the user already owns.

---

## Step 1 — Locate the project root

Find the root of the user's repository — the directory containing the `.sln` file, or the git root. All paths below are relative to that root.

If the workspace contains multiple repositories, ask the user which one to set up before continuing. This is the only question you may ask.

## Step 2 — Create the instructions directory

Create `.github/instructions/` if it does not already exist. Do not delete or modify anything already inside it.

## Step 3 — Discover which files to install

Do not assume a fixed list of files. List the published directory and install everything in it.

List this endpoint:

```
https://api.github.com/repos/Sitefinity/sitefinity-ai-dev-guide/contents/.github/instructions?ref=main
```

It returns a JSON array. Take every entry whose `name` ends in `.instructions.md` and note its `download_url`.

If that endpoint is unreachable, fall back to browsing `https://github.com/Sitefinity/sitefinity-ai-dev-guide/tree/main/.github/instructions` and reading the file names from the page.

## Step 4 — Download them

Write each discovered file into `.github/instructions/` using its original file name.

Use your built-in fetch or web tool if you have one. If you do not, run the equivalent command in the terminal.

PowerShell:

```powershell
$api = "https://api.github.com/repos/Sitefinity/sitefinity-ai-dev-guide/contents/.github/instructions?ref=main"
New-Item -ItemType Directory -Force -Path ".github/instructions" | Out-Null

# Assign to a variable first — piping Invoke-RestMethod directly does not enumerate the array
$entries = Invoke-RestMethod -Uri $api -Headers @{ "User-Agent" = "sitefinity-ai-dev-guide-setup" }

foreach ($entry in $entries) {
  if ($entry.name -notlike "*.instructions.md") { continue }
  Invoke-WebRequest -Uri $entry.download_url -OutFile ".github/instructions/$($entry.name)" -UseBasicParsing
  Write-Host "installed $($entry.name)"
}
```

Bash:

```bash
api="https://api.github.com/repos/Sitefinity/sitefinity-ai-dev-guide/contents/.github/instructions?ref=main"
mkdir -p .github/instructions
curl -fsSL "$api" \
  | grep -o '"download_url": *"[^"]*\.instructions\.md"' \
  | sed 's/.*"\(https[^"]*\)"/\1/' \
  | while read -r url; do
      curl -fsSL "$url" -o ".github/instructions/$(basename "$url")"
      echo "installed $(basename "$url")"
    done
```

### Rules while downloading

- Overwrite existing `sitefinity-*.instructions.md` files — that is an intentional update to the latest version.
- Never modify, rename, or delete any file that does not start with `sitefinity-`. Those belong to the user.
- Never touch `.github/copilot-instructions.md` or `AGENTS.md`.

## Step 5 — Verify

Confirm that every file discovered in step 3 now exists in `.github/instructions/`, is non-empty, and begins with YAML frontmatter containing an `applyTo` key. If any file is missing or empty, retry that file once, then report the failure.

## Step 6 — Report

Tell the user, substituting the actual count and file names you installed:

```
┌─ Sitefinity AI Dev Guide Installed ──────────────────┐
│  ✓ <count> instruction files → .github/instructions/ │
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
