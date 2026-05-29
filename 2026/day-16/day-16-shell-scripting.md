# Day 16 – Shell Scripting Basics

## Task
Start your shell scripting journey — learn the fundamentals every script needs.

## Challenge Tasks

### Task 1: Your First Script
1. Create a file `hello.sh`
2. Add the shebang line `#!/bin/bash` at the top
3. Print `Hello, DevOps!` using `echo`
4. Make it executable and run it

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/29b12beef7a4dbfc04e4eccdb6f8a39df966f07d/2026/day-16/task1.png)

**Document:** What happens if you remove the shebang line? 
> _I can still see the output without any errors_

---

### Task 2: Variables
1. Create `variables.sh` with:
   
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/29b12beef7a4dbfc04e4eccdb6f8a39df966f07d/2026/day-16/task2.png)

3. Try using single quotes vs double quotes — what's the difference?
   
> - _Single ' ' - Treats text literally_
> - _Double " " - Expands variables_

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/29b12beef7a4dbfc04e4eccdb6f8a39df966f07d/2026/day-16/task2a.png)

---

### Task 3: User Input with read
1. Create `greet.sh` that:
   - Asks the user for their name using `read`
   - Asks for their favourite tool
   - Prints: `Hello <name>, your favourite tool is <tool>`
     
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/29b12beef7a4dbfc04e4eccdb6f8a39df966f07d/2026/day-16/task3.png)

---

### Task 4: If-Else Conditions
1. Create `check_number.sh` that:
   - Takes a number using `read`
   - Prints whether it is **positive**, **negative**, or **zero**
     
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/29b12beef7a4dbfc04e4eccdb6f8a39df966f07d/2026/day-16/task4.png)

2. Create `file_check.sh` that:
   - Asks for a filename
   - Checks if the file **exists** using `-f`
   - Prints appropriate message
     
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/29b12beef7a4dbfc04e4eccdb6f8a39df966f07d/2026/day-16/task4b.png)

---

### Task 5: Combine It All
Create `server_check.sh` that:
1. Stores a service name in a variable (e.g., `nginx`, `sshd`)
2. Asks the user: "Do you want to check the status? (y/n)"
3. If `y` — runs `systemctl status <service>` and prints whether it's **active** or **not**
4. If `n` — prints "Skipped."
   
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/29b12beef7a4dbfc04e4eccdb6f8a39df966f07d/2026/day-16/task5.png)
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/29b12beef7a4dbfc04e4eccdb6f8a39df966f07d/2026/day-16/task5a.png)

## Key Learnings
- Importance of shebang
- Variable usage
- Difference between single and double quotes
- Taking user input
- Writing conditions

---
