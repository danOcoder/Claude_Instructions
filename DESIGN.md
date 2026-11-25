# Design notes

Why this system is shaped the way it is. `README.md` is the operating manual; this is the
reasoning behind it. Nothing here is needed day to day.

## Loaded vs readable

Two mechanisms, easily conflated:

- **Loaded** — always in context, every message. Happens *only* through `CLAUDE.md` /
  `CLAUDE.local.md` and their `@` imports, naming explicit files. There is no "load this
  directory" syntax.
- **Readable** — Claude can go fetch it when relevant, at no cost until it does.

Attaching a folder as a working directory grants readability *and* permits external imports
to resolve, but it never loads anything by itself.

## Why the folder must be attached

An import is **external** when it resolves outside the session's working directories. Per the
Claude Code docs, external imports in a project-level memory file sit behind a one-time
approval dialog, and on desktop, external imports in user-scope files are skipped outright.
Since every file here lives outside the repo, every import into it is external.

Path syntax is not the lever — `@~/...` and `@../../...` are equally external. Nor is
`permissions.additionalDirectories`: that grants file *access*, which is necessary but not
sufficient.

The actual gate is `hasClaudeMdExternalIncludesApproved`, stored per project in
`~/.claude.json`. Observed 2026-09-09: it sat `false` with
`hasClaudeMdExternalIncludesWarningShown` also `false` — never prompted, so the import stayed
inert with no error and no dialog. Setting it `true` made all nine memory files load
(1.3k → 11.1k). If the dialog is ever declined, imports stay disabled and it never re-prompts.

Path spellings are tracked as separate project entries — `C:\...\repo`, `C:/.../repo` and
`C:\...\repo\subdir` each carry their own flag, and a session launched in a subdirectory reads
that subdirectory's entry.

**The failure mode is silence.** A context system that isn't loading looks identical to one
that is, until a fresh session needs a fact it doesn't have. `/context` token counts are the
only honest signal: an unexpanded import still lists its parent file at ~30 tokens.

## Two axes, not one

The layout looks like one hierarchy but encodes two independent properties:

```
scope of applicability   everywhere │ this repo │ this scope   → which layer
audience                 team       │ personal                 → which file
```

That's why `PROJECT.md` and `PERSONAL.md` sit side by side at the same layer:

- **`PROJECT.md`** holds only what a teammate could read and act on unchanged — topology,
  build commands, per-app divergences, enforced formats. Nothing in it stops being true when
  someone else reads it.
- **`PERSONAL.md`** holds the rest at the same scope: routes into your own notes, your
  planning process, preferences that are yours rather than the team's.

Keeping them apart makes the eventual team handover a file move rather than surgery.

## Why `INDEX.md` exists

It keeps the in-repo stub to one line, written once and never edited. Adding, removing or
reordering a layer is then an edit inside this folder, and recovering after a fresh clone only
ever means restoring that same single line.

`INDEX.md` deliberately does **not** import `CLAUDE.md`. Routing the working agreements
through a repo-scoped file would mean they load only in repos that have an index — so a
scratch clone or a personal side project would silently lose "never stage or commit", the
highest-stakes rule in the system, in exactly the unfamiliar contexts where you'd least
notice.

## The repo stub

`<repo>/CLAUDE.local.md` is one import line and no content — the only file this system puts
inside a repo. It has to be there: memory files are discovered in the working directory, its
parents, or `~/.claude`, never an unrelated folder.

It's untracked and ignored via `.git/info/exclude`, which is local to the clone and never
committed, so no teammate's repo gains an entry.

| Situation | Stub | Exclude entry |
|---|---|---|
| Branch switch / merge / rebase | survives | survives |
| `git clean -fd` | survives — ignored files are skipped without `-x` | survives |
| `git clean -fdx` | **deleted** | survives |
| Fresh clone | **missing** | **missing** — both live in `.git/` |
| `git worktree add` | **missing** — untracked files aren't copied | survives (shared common dir) |

Untracked files aren't touched by `git checkout`, so there is no per-branch step.

## Scope docs and human-facing planning docs

If you keep a planning doc per scope, it and the scope doc are two artifacts with two
audiences:

- **The planning doc** — human-facing. Outcome, a diagram of how the pieces connect, contract
  tables, open unknowns. What you read to see the shape, and what colleagues can review.
- **`projects/<name>/scopes/<scope>.md`** — Claude-facing. Cites the planning doc, then carries what a diagram
  can't: exact machine names, merge-vs-replace semantics, the gotcha that costs an afternoon.

The scope doc is the working surface; it gets appended to as facts are verified in source.

## The corrections log

Every scope doc ends with one. It records facts that were wrong and keeps them after they're
fixed.

This is not tidiness — it's the highest-leverage section in the file. "The field is spelled
`damage_category`; the obvious spelling 400s" prevents a specific hour of debugging every time
a fresh session reaches for the wrong one.

## Adopting this on a team

The layers are the portable part; only the contents are personal.

- **`PROJECT.md` is the natural team artifact** — verified repo facts that currently live in
  people's heads. Committing it as the repo's own `CLAUDE.md` is the first step.
- **`CLAUDE.md` (working agreements) stays personal.** "Never commit" is your rule.
- **`PERSONAL.md` and the scope docs stay personal** — per-person, and per-scope.

### When the team file lands

Nothing breaks. Because the stub is `CLAUDE.local.md`, a tracked `CLAUDE.md` arriving beside
it isn't a conflict — both are discovered and both load, with `CLAUDE.local.md` appended after
`CLAUDE.md` in the same directory.

Only the contents change:

1. `PROJECT.md` content graduates into the team's `CLAUDE.md`.
2. Drop `PROJECT.md` from `INDEX.md`, so the same facts aren't carried twice — the drift
   pattern this system exists to prevent.
3. `PERSONAL.md` and `ACTIVE_SCOPE.md` stay exactly where they are.

**Never add your imports by editing the tracked team file.** Local edits to a tracked file
show up in `git status` and eventually get committed by accident. That's what the local stub
is for.
