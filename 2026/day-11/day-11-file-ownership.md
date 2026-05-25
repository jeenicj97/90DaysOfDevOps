# Day 11 Challenge
---
## Files & Directories Created

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/3f2080132bfe4f36303db80eef9154cef746222a/2026/day-11/task1.png)

| Section | Meaning |
|---------|---------|
| `drwxr-xr-x` | Permissions |
| `2` | Link Count |
| `jeenicj` | Owner |
| `jeenicj` | Group |
| `4096` | File Size |
| `May 19 14:18` | Modified Date |
| `devops` | Directory Name |


`Owner`
- The main user who controls the file
- Usually the creator of the file
- Can modify permissions and ownership (with sudo)

  
`Group`
- Multiple users can belong to the same group
- Used to share file access among team members

---

## Ownership Changes

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/3f2080132bfe4f36303db80eef9154cef746222a/2026/day-11/task2.png)
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/3f2080132bfe4f36303db80eef9154cef746222a/2026/day-11/task3.png)
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/3f2080132bfe4f36303db80eef9154cef746222a/2026/day-11/task4.png)
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/3f2080132bfe4f36303db80eef9154cef746222a/2026/day-11/task5.png)
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/3f2080132bfe4f36303db80eef9154cef746222a/2026/day-11/task6.png)

`Observation:`
- Without -R, chown only changes the owner of the top-level directory heist-project/ alone - the files and folders inside remain unchanged.
- With -R, it changes ownership of everything
  
---

## Commands Used
- ls -l
- touch
- mkdir
- mkdir -p
- sudo useradd
- sudo groupadd
- sudo chown
- sudo chgrp
- sudo chown -R
- ls -lR

---

## What I Learned
- Every Linux file has both an owner and a group.
- chown changes ownership while chgrp changes group ownership.
- Recursive ownership (-R) is useful for managing application directories and projects.
