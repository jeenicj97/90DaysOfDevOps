# Git Commands Reference

## Setup & Config
```
$ git config --global user.name "jeenicj97"
$ git config --global user.email "jeenicj97@gmail.com"
```
  Sets your Git username globally.

## Basic Workflow
- git init  
  Initializes a new Git repository.

- git add <file>  
  Stages changes for commit.

- git commit -m "Message"  
  Saves staged changes with a message.

- git branch feature-1
  Creates a branch

- git checkout feature-1
  Switches branch

- git checkout -b feature-2
  Creates + Switches branch in one step

- git switch feature-1
- git switch main
  Modern Alternative for switching

```
`git switch` is newer and simpler - it's purpose-built for switching branches only. `git checkout` is older and does multiple things (switch branches, restore files, etc.). git switch is clearer and safer because it doesn't overload the command with unrelated functionality.
```
- git branch -d feature-2
  Deletes branch

- git clone https://github.com/jeenicj97/hellogitworld.git
  Use GitHub’s fork button, then clone your fork to local using the above command

- After forking, how do you keep your fork in sync with the original repo?
     ```
      git remote add upstream https://github.com/ORIGINAL-OWNER/repo.git
      git fetch upstream
      git merge upstream/main
      git push origin main
    ```


  
## Viewing Changes
- git status  
  Shows current repo state.

- git log  
  Displays commit history.

- git log --oneline
  Compact History

- git branch
  List branches

### PUSH
- git remote add origin https://github.com/jeenicj97/practice-repo.git
  Connect local repo to Github repo

- git push origin master
- git push origin feature-1
  Push branches to Git Repo

### PULL
- git pull origin master


