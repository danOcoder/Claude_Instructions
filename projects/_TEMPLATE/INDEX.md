# <PROJECT> — context index

Copy this folder to `projects/<name>/`, add `PROJECT.md` and `PERSONAL.md` inside it,
then point the repo's `CLAUDE.local.md` at its `INDEX.md`:

```
@~/Claude_Instructions/projects/<name>/INDEX.md
```

Add or remove a layer by editing the imports below. The stub inside the repo never
changes.

Do **not** import `~/Claude_Instructions/CLAUDE.md` here — it already loads in every
project via `~/.claude/CLAUDE.md`. Importing it again would load it twice in this repo,
and would not help the repos that have no index.

@~/Claude_Instructions/projects/<name>/PROJECT.md
@~/Claude_Instructions/projects/<name>/PERSONAL.md
@~/Claude_Instructions/projects/<name>/ACTIVE_SCOPE.md
