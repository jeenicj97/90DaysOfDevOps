# Day 18 – Shell Scripting: Functions & intermediate Concepts

---

## Challenge Tasks

### Task 1: Basic Functions
1. Create `functions.sh` with:
   - A function `greet` that takes a name as an argument and prints `Hello, <name>!`
   - A function `add` that takes two numbers and prints their sum
   - Call both functions from the script
```
#!/bin/bash

greet() {

        name=$1
        echo "Hello, $name!"
}

add() {

        num1=$1
        num2=$2
        sum=$((num1+num2))
        echo "Sum of numbers is: "$sum
}

greet "$1"
add "$2" "$3"
```
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/0273904163b6f01916f63130df0ee69f5530a217/2026/day-18/task1.png)
 

---

### Task 2: Functions with Return Values
1. Create `disk_check.sh` with:
   - A function `check_disk` that checks disk usage of `/` using `df -h`
   - A function `check_memory` that checks free memory using `free -h`
   - A main section that calls both and prints the results
```
#!/bin/bash

check_disk() {

        echo "===Disk Usage==="
        df -h /
        #echo "======END OF RESULT======"
}

check_memory() {

        echo "-----------------------------------------"
        echo "===Memory Usage==="
        free -h
        echo "======END OF RESULT======"
}

echo "System Report"
echo "-------------"
check_disk
check_memory
```
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/0273904163b6f01916f63130df0ee69f5530a217/2026/day-18/task2.png)

---

### Task 3: Strict Mode — `set -euo pipefail`
1. Create `strict_demo.sh` with `set -euo pipefail` at the top
2. Try using an **undefined variable** — what happens with `set -u`?
3. Try a command that **fails** — what happens with `set -e`?
4. Try a **piped command** where one part fails — what happens with `set -o pipefail`?

```
#!/bin/bash

set -euo pipefail

echo "This should give error $name" #undefined variable -> set -u

ls king.txt #failing command -> set -e

cat king.txt | grep "hi" #failing pipeline -> set -o pipefail
```

**Document:** What does each flag do?
- `set -e` → Exits immediately when any command fails
- `set -u` → Exists immediately when an undefined variable is used
- `set -o pipefail` → Exists if any command in the pipe fails, not just the last one

---

### Task 4: Local Variables
1. Create `local_demo.sh` with:
   - A function that uses `local` keyword for variables
   - Show that `local` variables don't leak outside the function
   - Compare with a function that uses regular variables
     
```
#!/bin/bash


local_var() {

        local age=28
        echo "Number is: $num"
}

regular_var() {

        name=Jeeni
        echo "Name is: $name"

}

echo "=====Local Variable====="
local_var $num

echo "=====Regular Variable====="
regular_var $name
```

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/0273904163b6f01916f63130df0ee69f5530a217/2026/day-18/task4.png)

---

### Task 5: Build a Script — System Info Reporter
Create `system_info.sh` that uses functions for everything:
1. A function to print **hostname and OS info**
2. A function to print **uptime**
3. A function to print **disk usage** (top 5 by size)
4. A function to print **memory usage**
5. A function to print **top 5 CPU-consuming processes**
6. A `main` function that calls all of the above with section headers
7. Use `set -euo pipefail` at the top

```
#!/bin/bash

set -euo pipefail

hostname_os() {

        echo "====hostname===="
        hostname
        echo "====OS info===="
        uname -a

}

uptime_data() {

        echo "====uptime===="
        uptime -p
        echo
}

disk_usage() {

        echo "====disk usage===="
        df -h | sort -k2 -h | tail -n 5
        echo
}

memory_usage() {

        echo "===memory usage==="
        free -h
        echo
}

cpu_process() {

        echo "===top 5 cpu consuming processes"
        ps -eo pid,comm,%cpu --sort=-%cpu | head -n 6
        echo
}

main() {

        hostname_os
        uptime_data
        disk_usage
        memory_usage
        cpu_process

}

main
```
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/0273904163b6f01916f63130df0ee69f5530a217/2026/day-18/task5.png)


---

## What you learned (3 key points)
 - Learned how set -euo pipefail makes Bash scripts safer by stopping on errors, catching      undefined variables, and failing pipelines correctly.
 - Made the mistake of naming a function the same as a command (e.g., hostname()), which       caused infinite recursion.
 - Understood the difference between local variables (scoped inside functions) and regular    variables (global, leak outside functions).

---


Happy Learning!
**TrainWithShubham**
