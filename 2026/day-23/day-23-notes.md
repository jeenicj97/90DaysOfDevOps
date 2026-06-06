# Day 23 – Git Branching & Working with GitHub

---

## Challenge Tasks

### Task 1: Understanding Branches
Answer these in your `day-23-notes.md`:
1. What is a branch in Git?
- `A branch is a pointer to a specific commit, allowing you to work on code independently`
2. Why do we use branches instead of committing everything to `main`?
- `We use branches as they isolate features, fixes or experiments so you don't break main`
3. What is `HEAD` in Git?
- `A git is a pointer that indicates which branch(or commit) you are currently on`
4. What happens to your files when you switch branches?
- `Git updates your working directory to match the branch's files. Files unique to the new branch appears; files from the old branch disapears`

---

### Task 2: Branching Commands — Hands-On
In your `devops-git-practice` repo, perform the following:
1. List all branches in your repo
2. Create a new branch called `feature-1`
3. Switch to `feature-1`
4. Create a new branch and switch to it in a single command — call it `feature-2`
5. Try using `git switch` to move between branches — how is it different from `git checkout`?
6. Make a commit on `feature-1` that does **not** exist on `main`
7. Switch back to `main` — verify that the commit from `feature-1` is not there
8. Delete a branch you no longer need
9. Add all branching commands to your `git-commands.md`

```
`git switch` is newer and simpler - it's purpose-built for switching branches only.
`git checkout` is older and do multiple things (switch branches, restore files, etc.)
```
---

### Task 3: Push to GitHub
1. Create a **new repository** on GitHub (do NOT initialize it with a README)
2. Connect your local `devops-git-practice` repo to the GitHub remote
3. Push your `main` branch to GitHub
4. Push `feature-1` branch to GitHub
5. Verify both branches are visible on GitHub
6. Answer in your notes: What is the difference between `origin` and `upstream`?
* `Origin: your fork/clone(your copy)`
* `Upstream: the original repo you forked from`

---

### Task 4: Pull from GitHub
1. Make a change to a file **directly on GitHub** (use the GitHub editor)
2. Pull that change to your local repo
3. Answer in your notes: What is the difference between `git fetch` and `git pull`?
* `git fetch: downloads changes from GitHub but doesn't merge`
* `git pull: fetches + merges into your current branch`
---

### Task 5: Clone vs Fork
1. **Clone** any public repository from GitHub to your local machine
2. **Fork** the same repository on GitHub, then clone your fork
3. Answer in your notes:
   - What is the difference between clone and fork?
     * `Clone: downloads repo locally`
     * `Fork: creates copy of repo into your GitHub`
   - When would you clone vs fork?
     * `Clone: when you want to work locally`
     * `Fork: when you want to contribute to someone else's repo or maintain your own version`
   - After forking, how do you keep your fork in sync with the original repo?
     ```
      git remote add upstream https://github.com/ORIGINAL-OWNER/repo.git
      git fetch upstream
      git merge upstream/main
      git push origin main
     ```

---
