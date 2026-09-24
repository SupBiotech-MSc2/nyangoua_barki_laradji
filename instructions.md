# Session 1 Guided Exercise — Group Notes (Groups of 3)

**Topic:** Version Control & Working Locally
**Format:** Groups of 3, one shared repo per group
**Counts toward:** Group Project grade (50%, shared with Session 2 exercise)

---

## 0. Before You Start

- Confirm each group member has working GitHub authentication (PAT or SSH key). 
  If someone's still stuck, sort it out now; the first push below is your live check.
- Pick one person to create the shared repo on GitHub (call them **Driver 1**).
  The other two are **Driver 2** and **Driver 3**.

---

## Keep a Work Log Throughout (all sections)

This log is **individual, not shared** — each person keeps a log of only the
commands *they* ran locally, in their own file: `worklog-<yourname>.txt`.

After each command, append its output to your own log file using the shell's
`>>` append operator — **not** `>`, which would overwrite the log each time.

Careful: plain `>>` redirects the output to the file *instead of* showing it in
your terminal, so you'd stop seeing what each command actually printed. Use
`tee -a` instead — it appends to the file **and** still prints to the terminal,
so you get both:

```
git status | tee -a worklog-<yourname>.txt
git add protocol.md
git commit -m "feat: add team note from <yourname>" | tee -a worklog-<yourname>.txt
```

(If you'd rather stick with plain `>>`, that's fine too — just keep
`worklog-<yourname>.txt` open in a separate terminal tab or editor with
auto-reload so you can check what landed in it as you go, since you won't see
it on screen otherwise.)

Each person's log should end up reflecting only the git commands *that person*
ran locally on their own machine — don't copy commands from a teammate into
your log, and don't merge the three logs into one.

At the end of the exercise (Section 6), each person stages and commits their
own log file like any other change.

## 1. Get the Repo (init vs. clone)

- **Driver 1:** create the group's repo on GitHub **from the course template**
  (use the "Use this template" button on the template repo, not "New repository"
  from scratch — this gives you the starter README/structure for the exercise),
  then `git clone` the new repo locally.
- **Driver 1:** add your group members as contributors to the group's repo.
- **Driver 2 & 3:** `git clone` the same URL into your own machines.

Checkpoint: all three of you have a local copy with commit history, not just the files.

---

## 2. Staging & the First Commit

The repo template includes `protocol.md` — a short CRISPR knockout screening
protocol. This is the shared file your group will work in throughout the
exercise.

- Each person adds **one line** with their name under the "Team Notes" section
  at the bottom of `protocol.md` (e.g. a one-line comment on a step you'd tweak).
- `git status` → see `protocol.md` as modified.
- `git add protocol.md` → `git status` again → see it staged.
- `git commit -m "feat: add team note from <yourname>"` (semantic commit format:
  `type: short description`; use `feat`, `fix`, `docs`, `refactor`, `test`, or `chore`).

---

## 3. The Daily Loop: add → commit → push → pull

- **Driver 1** pushes first: `git push origin main`.
- **Driver 2** tries to push next — this will normally be rejected because Driver 1
  is now ahead. Run `git pull origin main` first, then push.
- **Driver 3** repeats the same pull-then-push sequence.

Checkpoint: `protocol.md` on GitHub has all three Team Notes lines, and everyone
has pulled everyone else's.

---

## 4. Undo & Rollback Practice

Three different tools, three different situations. Everyone does all three,
on their own machine.

**a) `git restore` — throw away an uncommitted edit**
- Add a deliberately wrong line to `protocol.md` (e.g. `**Status:** BROKEN`
  under Team Notes), don't stage it.
- `git status` → shows it as modified.
- `git restore protocol.md` → the edit is gone, back to your last commit.
- `git status` → clean again.

**b) `git reset` — undo a local commit you haven't pushed yet**
- Make a small edit to `protocol.md`, then `git add` and commit it with the
  message `"test: throwaway commit"`.
- `git log --oneline` → see that throwaway commit on top.
- `git reset HEAD~1` → moves your branch pointer back one commit. The commit is
  gone from history, and your edit returns to unstaged (check with `git status`).
- `git restore protocol.md` (or `git restore --staged` first if you'd `git add`ed
  again) to clean it up fully.
- Only ever do this to commits that are still local and unpushed — never on
  commits already on `origin/main`.

**c) `git revert` — safely undo a commit that's already shared**
- Pick one of the small commits your group already pushed to `main` earlier
  (e.g. your Section 2 Team Notes commit — find its hash with `git log --oneline`).
- `git revert <hash>` → Git opens an editor with a pre-filled message; save and
  close it. This creates a **new** commit that undoes the old one, rather than
  rewriting history.
- `git push origin main`.
- `git log --oneline` → notice both the original commit and the revert commit
  are still there, and `protocol.md` no longer has your Team Notes line.
- Add your Team Notes line back with a fresh commit afterwards so Section 2's
  work isn't lost for the rest of the exercise.

Checkpoint: as a group, agree on the difference between the three — which one
rewrites history, which one is safe on shared commits, and which one only ever
touches the working directory.

---

## 5. Identifying Commits

- `git log` → full history: hash, author, date, message for every commit so far.
- `git log --oneline` → the same thing, condensed to one line per commit.
- Pick any hash from that list and run `git show <hash>` → see exactly what that
  commit changed, line by line.
- Compare that to what `git diff` and `git diff --staged` showed you back in
  Section 2 — `git show` is the same kind of diff, but for a commit that's
  already been made, instead of your current uncommitted/staged changes.

Checkpoint: everyone can find a specific commit by its hash and describe what
it changed, without needing to guess from the message alone.

---

## 6. Branching

- Each person creates their own feature branch: `git checkout -b feature/<yourname>-idea`.
- `git branch` → confirm you're now on your new branch (marked with `*`) and
  `main` still exists alongside it.
- Make a small edit inside your own branch (e.g. tweak a step, add a reagent to
  `protocol.md`).
- `git add`, commit with a semantic message, and push the branch:
  `git push origin feature/<yourname>-idea`.
- `git switch main` → move back to `main` and confirm (`git status`) that your
  branch's edit isn't there — it only exists on `feature/<yourname>-idea`.
- `git switch feature/<yourname>-idea` → switch back to your branch.
- Keep `main` untouched during this step — that's the point of branching.

---

## 7. Merge vs. Rebase (local-only practice)

Do this on your own feature branch from Section 6 — **don't** rebase anything
you've already pushed and shared, that's the one rule to remember here.

- On your feature branch, make one more small local commit (don't push it yet).
- `git log --oneline main` vs. `git log --oneline` (on your branch) → see how
  your branch now has commits `main` doesn't.
- `git rebase main` (run from your feature branch) → replays your branch's
  commits on top of the current tip of `main`, giving a linear history.
- `git log --oneline --graph` → compare what this looks like versus the merge
  commit you'll see in Section 8 — rebase produces a straight line, merge
  produces a branch-and-rejoin shape.
- Since this branch and its rebased commit are still local/unpushed at this
  point, this is safe. Once you `push` and a teammate might have those original
  commits too, switch to `merge` instead — never rebase shared history.

---

## 8. The Planted Conflict

Goal: make Git show a conflict, then resolve it together. Follow these steps
in order.

**Driver 1:**
```
git checkout -b feature/status-v2
```
Edit line 3 of `protocol.md` to: `**Status:** Draft v2`
```
git add protocol.md
git commit -m "chore: bump status to draft v2"
git checkout main
git merge feature/status-v2
git push origin main
```
(No conflict yet — `main` now has `Draft v2`.)

**Driver 2** (start this only after Driver 1 has pushed):
```
git checkout -b feature/status-review
```
Edit line 3 of `protocol.md` to: `**Status:** Under review`
```
git add protocol.md
git commit -m "chore: mark status as under review"
git checkout main
git pull origin main
git merge feature/status-review
```
This last `merge` fails with a conflict — Git can't tell whether line 3 should
say `Draft v2` or `Under review`.

**Whole group, together, now:**
1. Open `protocol.md`. Line 3 will look like this:
   ```
   <<<<<<< HEAD
   **Status:** Draft v2
   =======
   **Status:** Under review
   >>>>>>> feature/status-review
   ```
2. Decide as a group what the line should actually say, then replace all five
   lines above (markers included) with just that one final line, e.g.:
   ```
   **Status:** Under review
   ```
3. Save the file, then:
   ```
   git add protocol.md
   git commit
   git push origin main
   ```
   (`git commit` with no `-m` opens an editor with a pre-filled merge message —
   just save and close it.)

Checkpoint: `git log --oneline` on `main` shows a merge commit, and line 3 of
`protocol.md` contains no `<<<<<<<`/`=======`/`>>>>>>>` markers.

---

## 9. Wrap-Up Check (do this together before moving on)

- `git log --oneline --graph --all` — can everyone in the group explain what each
  commit and branch point represents, including the revert and merge commits?
- `git status` on `main` for all three of you — should be clean, nothing pending.
- Confirm all three feature branches were merged (or note which weren't, and why).
- Each person adds and commits their own `worklog-<yourname>.txt` if they
  haven't already (see "Keep a Work Log Throughout" above) and pushes it.

---

## What We're Grading

- Repo shows commits from all three group members (`git log` author field).
- Commit messages follow the `type: description` semantic format.
- At least one branch was created and merged per person.
- Evidence in `git log` that `restore`, `reset`, and `revert` were all practiced
  (a discarded edit, a removed throwaway commit, and a visible revert commit).
- The planted conflict was actually resolved through Git (not worked around by
  deleting and re-adding the file, or by one person just overwriting the other's
  work outside Git).
- Each group member has their own `worklog-<yourname>.txt` in the repo,
  reflecting only the commands *they* ran locally (built with `>>`/`tee -a`,
  not overwritten with `>`, and not copied from teammates).
- Final `main` is in a clean, working state.
