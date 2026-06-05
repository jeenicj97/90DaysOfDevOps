# Day 22 – Introduction to Git: Your First Repository

---

## Challenge Tasks

### Task 1: Install and Configure Git
1. Verify Git is installed on your machine
2. Set up your Git identity — name and email
3. Verify your configuration

```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~ (master)
$ git --version
git version 2.49.0.windows.1
```

---

### Task 2: Create Your Git Project
1. Create a new folder called `devops-git-practice`
2. Initialize it as a Git repository
3. Check the status — read and understand what Git is telling you
4. Explore the hidden `.git/` directory — look at what's inside

```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~ (master)
$ cd devops-git-practice/

Jeeni@DESKTOP-BG3MAVI MINGW64 ~/devops-git-practice (master)
$ git init
Initialized empty Git repository in C:/Users/Jeeni/devops-git-practice/.git/
```
---

### Task 3: Create Your Git Commands Reference
1. Create a file called `git-commands.md` inside the repo
2. Add the Git commands you've used so far, organized by category:
   - **Setup & Config**
   - **Basic Workflow**
   - **Viewing Changes**
3. For each command, write:
   - What it does (1 line)
   - An example of how to use it

```
git-commands.md
```
---

### Task 4: Stage and Commit
1. Stage your file
2. Check what's staged
3. Commit with a meaningful message
4. View your commit history

```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~/devops-git-practice (master)
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        git-commands.md

nothing added to commit but untracked files present (use "git add" to track)

Jeeni@DESKTOP-BG3MAVI MINGW64 ~/devops-git-practice (master)
$ git add git-commands.md
warning: in the working copy of 'git-commands.md', LF will be replaced by CRLF the next time Git touches it

Jeeni@DESKTOP-BG3MAVI MINGW64 ~/devops-git-practice (master)
$ git commit -m "Adding file"
[master b1fad35] Adding file
 1 file changed, 26 insertions(+)
 create mode 100644 git-commands.md

Jeeni@DESKTOP-BG3MAVI MINGW64 ~/devops-git-practice (master)
$ git status
On branch master
nothing to commit, working tree clean

Jeeni@DESKTOP-BG3MAVI MINGW64 ~/devops-git-practice (master)
$ git log
commit b1fad3555289a1f4eae46351443da074091a5604 (HEAD -> master)
Author: jeenicj97 <jeenicj97@gmail.com>
Date:   Fri Jun 5 22:04:48 2026 +0530

    Adding file

commit 2d2c1ea1ec0f707113bfe97517a24c9e9699c2e3
Author: jeenicj97 <jeenicj97@gmail.com>
Date:   Fri Jun 5 21:51:57 2026 +0530

    Initial Commit

```
---

### Task 5: Make More Changes and Build History
1. Edit `git-commands.md` — add more commands as you discover them
2. Check what changed since your last commit
3. Stage and commit again with a different, descriptive message
4. Repeat this process at least **3 times** so you have multiple commits in your history
5. View the full history in a compact format

```
Jeeni@DESKTOP-BG3MAVI MINGW64 ~/devops-git-practice (master)
$ git log --oneline
6231c1d (HEAD -> master) Adding file again1
d1eaacb Adding file again
b1fad35 Adding file
2d2c1ea Inital Commit
```
---

### Task 6: Understand the Git Workflow
Answer these questions in your own words:
1. What is the difference between `git add` and `git commit`?
- `git add : moves file from local to staging area and git commit saves them permanently in the repo`
3. What does the **staging area** do? Why doesn't Git just commit directly?
- `It lets you review changes before committing`
4. What information does `git log` show you?
- `Shows commit history with IDs and commit message`
5. What is the `.git/` folder and what happens if you delete it?
- `Contains all metadata & history. If deleted, git will have no memory of your project` 
6. What is the difference between a **working directory**, **staging area**, and **repository**?
- `Working directory: actual local files`
- `staging area: move file from local to staging area`
- `repository: like a folder which has files and history of commits` 

---
