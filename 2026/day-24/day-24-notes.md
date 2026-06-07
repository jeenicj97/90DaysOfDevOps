# Day 24 – Advanced Git: Merge, Rebase, Stash & Cherry Pick

---

## Challenge Tasks

### Task 1: Git Merge — Hands-On
1. Create a new branch `feature-login` from `main`, add a couple of commits to it
2. Switch back to `main` and merge `feature-login` into `main`
3. Observe the merge — did Git do a **fast-forward** merge or a **merge commit**?
```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~/practice-repo (master)
$ git merge feature-login
Updating e1615d7..782242f
Fast-forward
 app.log  | 1 +
 hello.py | 1 +
 2 files changed, 2 insertions(+)
 create mode 100644 app.log
 create mode 100644 hello.py
```
5. Now create another branch `feature-signup`, add commits to it — but also add a commit to `main` before merging
6. Merge `feature-signup` into `main` — what happens this time?
```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~/practice-repo (master)
$ git log
commit 132caf4f4c6568273a54a69be9d4ae4383b077fc (HEAD -> master)
Merge: 137bccb ebc90b9
Author: jeenicj97 <jeenicj97@gmail.com>
Date:   Sat Jun 6 22:55:06 2026 +0530

    Merge branch 'feature-signup'
     for demo
```
7. Answer in your notes:
   - What is a fast-forward merge?
     * Happens when your main branch has no new commits since you branched off; Git simply moves the pointer forward to your latest commit without creating a new merge      commit. 
   - When does Git create a merge commit instead?
     * Created when branches have diverged (both have new, different commits); Git must generate a new commit to tie the two histories back together.[Merge Commit ID is created]
   - What is a merge conflict? (try creating one intentionally by editing the same line in both branches)
     * Happens when the same line of code is changed differently on both branches, forcing Git to stop and ask you to manually choose which version to keep.
       
---

### Task 2: Git Rebase — Hands-On
1. Create a branch `feature-dashboard` from `main`, add 2-3 commits
2. While on `main`, add a new commit (so `main` moves ahead)
3. Switch to `feature-dashboard` and rebase it onto `main`
4. Observe your `git log --oneline --graph --all` — how does the history look compared to a merge?
* `all my commits are in one line - with master not like below how it is with merge`
```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~/practice-repo (master)
$ git log --oneline --graph --all
* 081a520 (HEAD -> master, feature-dashboard) 3rd commit
* 0ac0453 added 2nd filr
* eecb26c added js file
* 3a870e3 amended master file
*   aed3d70 (origin/master, origin/HEAD) fixed conflict
|\
| * c6142b1 (origin/feature-signup, feature-signup) Amending file
* | f6b9e0b amnded file from master
* | 132caf4 Merge branch 'feature-signup'  for demo
|\|
| * ebc90b9 added file2
| * 84d4f47 added txt file
* | 137bccb amended log file
|/
```
6. Answer in your notes:
   - What does rebase actually do to your commits?
     * `Rebase lifts your branch commits and replants them on top of another branch's latest commit - giving them a new base point`
   - How is the history different from a merge?
     * `Rebase gives a clean straight line in git log.`
     * `Merge shows branching lines with a merge commit where two branches joined`
   - Why should you **never rebase commits that have been pushed and shared** with others?
     * `Rebase rewrites commit IDs. If others have pulled your commits and you rebase, their history and yours conflit - causing chaos for the whole team`
   - When would you use rebase vs merge?
     | Situation | Use |
     |---|---|
     | Cleaning up before a Pull Request | Rebase |
     | Bringing teammate's work into your branch | Rebase |
     | Combining completed feature into main | Merge |
     | Working on a shared/public branch | Merge |
     
     > **Simple rule: rebase to tidy, merge to combine.**
     > * Once a commit is on GitHub and someone else has pulled it - that commit ID is shared property
     > * Rebase changes the ID - now you and your teammate have different versions of the same commit
---

### Task 3: Squash Commit vs Merge Commit
1. Create a branch `feature-profile`, add 4-5 small commits (typo fix, formatting, etc.)
2. Merge it into `main` using `--squash` — what happens?
3. Check `git log` — how many commits were added to `main`?
* `Not seeing any commits`
```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~/practice-repo (master)
$ git merge --squash feature-profile
Updating 081a520..1a6e033
Fast-forward
Squash commit -- not updating HEAD
 backend.py  | 1 +
 data.py     | 1 +
 frontend.py | 1 +
 3 files changed, 3 insertions(+)
 create mode 100644 backend.py
 create mode 100644 data.py
 create mode 100644 frontend.py

Jeeni@DESKTOP-BG3MAVI MINGW64 ~/practice-repo (master)
$ git log --oneline
081a520 (HEAD -> master, feature-dashboard) 3rd commit
0ac0453 added 2nd filr
eecb26c added js file
3a870e3 amended master file

```
5. Now create another branch `feature-settings`, add a few commits
6. Merge it into `main` **without** `--squash` (regular merge) — compare the history
7. Answer in your notes:
   - What does squash merging do?
   - When would you use squash merge vs regular merge?
   - What is the trade-off of squashing?

---

### Task 4: Git Stash — Hands-On
1. Start making changes to a file but **do not commit**
2. Now imagine you need to urgently switch to another branch — try switching. What happens?
3. Use `git stash` to save your work-in-progress
4. Switch to another branch, do some work, switch back
5. Apply your stashed changes using `git stash pop`
6. Try stashing multiple times and list all stashes
7. Try applying a specific stash from the list
8. Answer in your notes:
   - What is the difference between `git stash pop` and `git stash apply`?
   - When would you use stash in a real-world workflow?

---

### Task 5: Cherry Picking
1. Create a branch `feature-hotfix`, make 3 commits with different changes
2. Switch to `main`
3. Cherry-pick **only the second commit** from `feature-hotfix` onto `main`
4. Verify with `git log` that only that one commit was applied
5. Answer in your notes:
   - What does cherry-pick do?
   - When would you use cherry-pick in a real project?
   - What can go wrong with cherry-picking?

---

## Hints
- Visualize history: `git log --oneline --graph --all`
- To intentionally create a merge conflict: edit the **same line** of the **same file** on two branches
- Stash with a message: `git stash push -m "description"`
- Cherry-pick needs a commit hash — find it with `git log --oneline`

---
