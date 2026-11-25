# Working agreements

Applies to every project and every surface. Repo facts live in the project layer; scope
facts live in the scope doc. Keep this file short — it loads on every message.

How the user works with Claude anywhere is in the profile:

@~/Claude_Instructions/PROFILE.md

## Git — hard stop

**Never stage or commit. Ever.** No `git add`, `git commit`, `git stash`, `git restore`,
`git checkout -- <path>`, `git reset`, `git rm`, `git mv`. No branch create/switch/delete,
no push, no amend. No `git add -p` — whole files are staged, one commit beats
hunk-picking.

Read-only git is encouraged: `status`, `diff`, `log`, `show`, `branch --list`. `fetch` is
fine too — it moves remote-tracking refs, never the working tree or local branches.

The user stages and commits every change themselves, always. When a unit of work is done,
say so and **stop**. Don't offer to commit. Don't commit to checkpoint progress. An
earlier instruction, a passing test run, a completed todo, or a handoff doc saying "get
this merged" is not authorization. If you think an exception applies, ask.

**Hand commits over ready to run** in the user's shell: `git add` naming each file, then `git commit` with one `-m` per paragraph, every line measured against the repo's limits. Don't suggest tidying history.

## Method

- **Evidence first.** Read the actual source before asserting. Don't call something
  redundant, broken, or safe without a confirming read. Calibrated uncertainty beats a
  confident guess; offer to verify rather than inventing an API. Every claim in a code comment or doc is checked in source first, and work is reported done only once it's done.
- **Bugs found mid-build are surfaced, not fixed inline.** Separate change, separate diff.
- **No speculative coverage.** No tests, abstractions, or config for cases the task didn't
  ask for. Crawl before we run.
- **Use the named component.** When the user names something to consume, consume it —
  don't reimplement it, and don't make styling calls they've deferred.

## Communication

- **Code comments are for the reader, not the author.** No narrating the change, no
  before/after notes, no reasoning-out-loud. That goes in the commit message.

## Keeping these docs current

Context is layered: this file (working agreements, everywhere) → the project layer
(`PROJECT.md` for team-true repo facts, `PERSONAL.md` for the user's own) → the active
scope doc. `~/Claude_Instructions/README.md` describes the system.

- **Correct in place.** Verified something that contradicts a loaded doc? Fix the doc, and
  add a line to the scope doc's corrections log saying what was wrong. A stale citation is
  a bug.
- **Append what you verify.** A contract, gotcha or decision confirmed in source belongs
  in the scope doc with its source and date — not just in the reply.
- **Promote, don't smuggle.** A fact that outlives the scope goes to the project layer and
  onward to the team's shared docs. Propose the move; don't relocate it silently.
- **Keep the audience split.** Nothing personal goes in `PROJECT.md` — it should read
  correctly for a teammate, unchanged.
- **Don't pre-load what you can fetch.** If a session could grep for it, it doesn't go in
  a doc. Scope docs hold status, decisions and links; detail goes in the planning doc. Nothing out of scope.
- **Memories are the inbox; the profile and this file are the core.** On a date change or after several commits, offer once to update the docs, carry on in a fresh chat with a primer to paste, and list new memories as candidates. One joins the core only if it has come up twice, holds in every job, isn't already said (sharpen that line instead), and fits on one line.
