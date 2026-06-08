# Day 25 – Git Reset vs Revert & Branching Strategies

---

## Challenge Tasks

### Task 1: Git Reset — Hands-On
1. Make 3 commits in your practice repo (commit A, B, C)
2. Use `git reset --soft` to go back one commit — what happens to the changes?
- `C.py file moves to the staged area & HEAD moved to one commit before`
```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~/practice-repo (master)
$ git log --oneline -5
6c4d86e (HEAD -> master) Added B
9c9e565 Added A
016ba68 added bye.yaml
```

```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~/practice-repo (master)
$ git status
On branch master
Your branch is ahead of 'origin/master' by 11 commits.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   C.py
```
4. Re-commit, then use `git reset --mixed` to go back one commit — what happens now?

`C.py file moves to untracked stage & HEAD moved to one commit before`

```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~/practice-repo (master)
$ git log --oneline -5
6c4d86e (HEAD -> master) Added B
9c9e565 Added A
016ba68 added bye.yaml
```

```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~/practice-repo (master)
$ git status
On branch master
Your branch is ahead of 'origin/master' by 11 commits.
  (use "git push" to publish your local commits)

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        C.py
```
6. Re-commit, then use `git reset --hard` to go back one commit — what happens this time?
- `HEAD moves to previous commit & C.py is completely deleted`
```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~/practice-repo (master)
$ git add C.py
fatal: pathspec 'C.py' did not match any files
```

```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~/practice-repo (master)
$ git reset --hard HEAD~1
HEAD is now at 9c9e565 Added A
```

8. Answer in your notes:
   - What is the difference between `--soft`, `--mixed`, and `--hard`?
       - **`--soft`** — moves HEAD back but keeps your changes staged and ready to commit again
         - Use when: you committed too early and want to redo the commit message or add more files
      
      - **`--mixed`** (default) — moves HEAD back and unstages changes but keeps files on disk
        - Use when: you want to recommit with different files or split one commit into multiple
    
      - **`--hard`** — moves HEAD back and completely deletes all changes — files go back to that commit state
        - Use when: you want to completely throw away recent work and start fresh
         
   
   - Which one is destructive and why?
      - `--hard` is destructive — it permanently deletes uncommitted changes from your working directory
      - There is no undo — once you run it, those changes are gone forever
      - `--soft` and `--mixed` are safe — your actual file changes are never deleted
   - When would you use each one?
      - `--soft` → "I committed too soon, let me fix the commit"
      - `--mixed` → "I staged the wrong files, let me redo it"
      - `--hard` → "I made a mess, throw everything away and start clean"
         - Should you ever use `git reset` on commits that are already pushed?
    - Should you ever use git reset on commits that are already pushed?
        - **No** — never reset commits that are already pushed to a shared branch
        - Other team members have already pulled those commits — resetting rewrites history
          and causes conflicts for everyone
        - Instead use `git revert` — it creates a new commit that undoes the change
          without rewriting history, which is safe for shared branches
        
        > Rule of thumb: `git reset` is for local unpushed commits only.
        > `git revert` is for anything already pushed.

---

### Task 2: Git Revert — Hands-On
1. Make 3 commits (commit X, Y, Z)
2. Revert commit Y (the middle one) — what happens?
3. Check `git log` — is commit Y still in the history?
- `Commit Y is still visible in history`
```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~/practice-repo (master)
$ git log --oneline -5
5e01af7 (HEAD -> master) Revert "Y file"
a5f058c Z file
5281168 Y file
659e830 X file
```
5. Answer in your notes:
   - How is `git revert` different from `git reset`?
      | | `git revert` | `git reset` |
      |---|---|---|
      | What it does | Creates a new commit that undoes changes | Moves HEAD back, rewrites history |
      | Old commit | Still in history | Removed from history |
      | Safe for pushed commits | ✅ Yes | ❌ No |
      | Destructive | Never | `--hard` is destructive |
      | Team impact | No impact on others | Causes conflicts for teammates |
   
   - Why is revert considered **safer** than reset for shared branches?
      - `git reset` **rewrites history** - it removes commits from the log
      - If teammates already pulled those commits, their history no longer matches yours
      - This causes conflicts and confusion for the whole team
      - `git revert` **adds to history** — it never removes anything
      - Everyone's history stays consistent — they just pull the new revert commit

   - When would you use revert vs reset?
      - **Use `git revert` when:**
      - Commit is already pushed to a shared branch
      - Working in a team — others may have pulled your commits
      - You want a clear audit trail of what was undone and why
      - Production hotfix — need to undo something safely
    
    - **Use `git reset` when:**
      - Commit is only local, not pushed yet
      - You want to clean up messy commits before pushing
      - Working alone on a feature branch nobody else has

---

### Task 3: Reset vs Revert — Summary
Create a comparison in your notes:

| | `git reset` | `git revert` |
|---|---|---|
| **What it does** | Moves HEAD backwards to an earlier commit, optionally changing staging/working directory | Creates a new commit that undoes the changes of a previous commit |
| **Removes commit from history?** | Yes (hard), partially (soft/mixed) | No, original commit remains in history |
| **Safe for shared/pushed branches?** | ❌ Not safe — rewrites history | ✅ Safe — preserves history and adds a new commit |
| **When to use** | Local cleanup before pushing, fixing mistakes in private work | Undoing changes in shared/public history without breaking collaboration |

---

### Task 4: Branching Strategies
Research the following branching strategies and document each in your notes with:
- How it works (short description)
- A simple diagram or flow (text-based is fine)
- When/where it's used
- Pros and cons

1. **GitFlow** — develop, feature, release, hotfix branches
2. **GitHub Flow** — simple, single main branch + feature branches
3. **Trunk-Based Development** — everyone commits to main, short-lived branches
4. Answer:
   - Which strategy would you use for a startup shipping fast?
   - Which strategy would you use for a large team with scheduled releases?
   - Which one does your favorite open-source project use? (check any repo on GitHub)

---

### Task 5: Git Commands Reference Update
Update your `git-commands.md` to cover everything from Days 22–25:
- Setup & Config
- Basic Workflow (add, commit, status, log, diff)
- Branching (branch, checkout, switch)
- Remote (push, pull, fetch, clone, fork)
- Merging & Rebasing
- Stash & Cherry Pick
- Reset & Revert

---

## Hints
- `git reflog` is your safety net — it shows everything Git has done, even after a hard reset
- For branching strategies, look at how projects like Kubernetes, React, or Linux kernel manage branches

---

