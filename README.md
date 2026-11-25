# Claude Instructions

Instructions Claude Code loads automatically at the start of every session. Lives outside
every work repo, in a private git repo of its own; project folders other than `projects/_TEMPLATE/` stay local and are never committed.

**Do I need to attach this folder to each chat? No.** It's set once in
`~/.claude/settings.json` — see [Setup](#setup). Nothing is required per session.

---

## Why it's like this

`CLAUDE.md` loads on **every message**, so every word is paid for repeatedly. The
discipline that follows: _if Claude can go and fetch it, point at it; if it must always be
true, state it._

Facts stay true for different lengths of time, so they live in different files:

```
~/.claude/CLAUDE.md                  ← auto-discovered, EVERY project
  └─ CLAUDE.md                       Claude Code rules · changes yearly
       └─ PROFILE.md                 how you work, any surface · also pasted into claude.ai

<repo>/CLAUDE.local.md               ← auto-discovered, THIS repo only
  └─ projects/<name>/INDEX.md        the entry point — add/remove layers here
       ├─ PROJECT.md                 repo facts, true for the team · quarterly
       ├─ PERSONAL.md                repo facts, true only for you
       └─ ACTIVE_SCOPE.md            → scopes/<scope>.md · the current job · daily
```

Two entry points because there are two **reaches**: `~/.claude/CLAUDE.md` is the only file
read in every project, so it holds nothing employer- or repo-specific. `CLAUDE.local.md`
is read only in its own repo, so repo context can't leak elsewhere.

`DESIGN.md` covers the reasoning in full — audience split, team adoption, edge cases.

---

## Setup

Four steps: 1 and 2 once per machine, 3 and 4 once per repo. After these, **nothing is required per session.**

**1. Attach this folder — `~/.claude/settings.json`**

```json
{
  "permissions": {
    "additionalDirectories": ["C:\\Users\\<you>\\Claude_Instructions"]
  }
}
```

Required, not optional. Every import into this folder resolves outside the repo, which
makes it _external_, and external imports are skipped unless the folder is one of the
session's working directories. Without this, the project layers silently never load. This
is the persistent form of `--add-dir`; `/add-dir` is the per-session fallback if it's ever
lost.

**2. Load the working agreements — `~/.claude/CLAUDE.md`**

```
@~/Claude_Instructions/CLAUDE.md
```

**3. Per repo — `<repo>/CLAUDE.local.md`**

```
@~/Claude_Instructions/projects/<name>/INDEX.md
```

Copy `projects/_TEMPLATE/` to `projects/<name>/` first. Keep the stub out of git with
`.git/info/exclude` — a local file, never committed — not `.gitignore`:

```bash
printf '\n/CLAUDE.local.md\n' >> .git/info/exclude
```

`CLAUDE.local.md` is the documented home for private per-project instructions. It loads
_alongside_ a team-owned `CLAUDE.md`, so the two never collide.

**4. Approve external includes — once per repo**

The project stub imports files outside the repo, which Claude Code gates behind an approval.
Until it's granted, the import stays inert and **nothing warns you**. Check
`~/.claude.json` under `projects`:

```
hasClaudeMdExternalIncludesApproved
```

It must be `true` for the repo. Note that path spellings are tracked separately —
`C:\...\repo`, `C:/.../repo` and `C:\...\repo\subdir` are three distinct entries, and a
session launched in a subdirectory reads that subdirectory's entry. Back the file up before
editing it by hand.

### Check it worked

Run **`/context`** and look under **Memory files**. That is the only reliable check.

Expect one entry per file in the chain — nine for a project with a scope loaded. **Check the
token counts, not just the names:** an import that never expanded still lists its parent, and
a stub showing ~30 tokens is a raw `@` line, not loaded content. A working chain puts
**Memory files** in the 10k range; a broken one sits near 1k.

Don't infer it from anything else. An import that never expanded still appears in a system
reminder as a raw `@` line, which reads exactly like success. And don't test by asking a
question — if the answer is findable by `grep`, or from any other source the session can
reach, a pass proves nothing.

### New machine

Clone this repo to `~/Claude_Instructions`, then do Setup steps 1 and 2. The repo doesn't carry these:

| What | Where | Do this |
|---|---|---|
| Project folders | `projects/<name>/` | Ignored by git. Copy them over by hand, or start fresh from `projects/_TEMPLATE/` |
| MCP servers for the desktop app | `%APPDATA%\Claude\claude_desktop_config.json` | Recreate the block below with the new machine's paths |
| How you work, on claude.ai | claude.ai Settings → Your Claude → Instructions | Paste `PROFILE.md` |
| Skills | `Obsidian_Vault/Skills/` | Upload each skill folder to claude.ai; skip any tied to an old job |
| Plugins | claude.ai Customize → Plugins | Switch on the ones you use, off the rest |
| Git defaults | `~/.gitconfig` | `git config --global core.editor "code --wait"` (git opens VS Code and waits) and `git config --global push.autoSetupRemote true` (first push links the branch, no `-u`) |

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "cmd",
      "args": ["/c", "npx", "-y", "@modelcontextprotocol/server-filesystem",
               "C:\\Users\\<you>\\Projects", "C:\\Users\\<you>\\Obsidian_Vault"]
    },
    "obsidian": {
      "command": "npx",
      "args": ["@bitbonsai/mcpvault@latest", "C:\\Users\\<you>\\Obsidian_Vault"]
    }
  }
}
```

The filesystem server takes one argument per folder Claude may read; list the ones that machine needs. Merge this into the file rather than replacing it: the app keeps its own settings there too.

---

## Cheatsheet

| I want to                                       | Do this                                                                              |
| ----------------------------------------------- | ------------------------------------------------------------------------------------ |
| Switch scope                                    | Edit the `@` line in `projects/<name>/ACTIVE_SCOPE.md`. Nothing else moves                 |
| Work with no scope                              | Delete that `@` line. Everything above still loads                                   |
| Start a scope                                   | `cp projects/_TEMPLATE/scopes/_TEMPLATE.md projects/<name>/scopes/<TICKET>-name.md`, fill what applies, delete the rest |
| Close a scope                                   | Promote anything durable, then delete the scope doc and its action plans             |
| Start an action plan                            | `projects/<name>/scopes/<Scope>.action-plan.md`, next to the scope doc               |
| Add a fact the team needs                       | `projects/<name>/PROJECT.md` — only if it outlives the ticket                        |
| Add a fact only true for you                    | `projects/<name>/PERSONAL.md`                                                        |
| Add a rule about how you work                   | `CLAUDE.md` — only if it's true in every repo                                        |
| Onboard a repo                                  | Copy `projects/_TEMPLATE/` to `projects/<name>/`, then Setup step 3                                        |
| Recover after a fresh clone or `git clean -fdx` | Redo Setup step 3. Branch switches need nothing                                      |
| Confirm it's loading                            | `/context` → **Memory files**                                                        |

**Where a fact goes:**

| Fact                                 | Goes in                                             |
| ------------------------------------ | --------------------------------------------------- |
| "Never commit"                       | `CLAUDE.md` — true in every repo, forever           |
| "This app bypasses the shared proxy" | `PROJECT.md` — true next quarter, true for everyone |
| "Where my planning notes live"       | `PERSONAL.md` — true next quarter, only for you     |
| "The enum is spelled X, not Y"       | the scope doc — dead once this scope ships          |
| "That file is 400 lines"             | nowhere — Claude can read it                        |

**Ask Claude to keep these current.** It's instructed to correct docs in place, append
what it verifies to the scope doc, and propose promotions rather than moving facts
silently.
