# Day 17 – Shell Scripting: Loops, Arguments & Error Handling

## Task
Level up your scripting — use loops, handle arguments, and deal with errors.
 
---
          

## Challenge Tasks

### Task 1: For Loop
1. Create `for_loop.sh` that:
   - Loops through a list of 5 fruits and prints each one
   
```bash
#!/bin/bash

Fruits=('Apple' 'Orange' 'Banana' 'Kiwi' 'Guava')
for f in ${Fruits[@]}; do
        echo "$f"
done
```

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/0cd2279179c237509a4d16a41cdf5201464e2859/2026/day-17/task1.png)

2. Create `count.sh` that:
   - Prints numbers 1 to 10 using a for loop
     
```bash
#!/bin/bash

for i in {1..10}; do
        echo "$i"
done
```

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/0cd2279179c237509a4d16a41cdf5201464e2859/2026/day-17/task1a.png)

---

### Task 2: While Loop
1. Create `countdown.sh` that:
   - Takes a number from the user
   - Counts down to 0 using a while loop
   - Prints "Done!" at the end
     
```bash
#!/bin/bash

read -p "Enter a number: " num
while [[ $num -ge 0 ]]; do
        echo "$num"
        num=$((num-1))

done
echo "Done!"
```

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/0cd2279179c237509a4d16a41cdf5201464e2859/2026/day-17/task2.png)

---

### Task 3: Command-Line Arguments
1. Create `greet.sh` that:
   - Accepts a name as `$1`
   - Prints `Hello, <name>!`
   - If no argument is passed, prints "Usage: ./greet.sh <name>"
     
```bash
#!/bin/bash

if [ $# -eq 0 ];then
        echo "Usage: ./greet.sh"
        exit 1
fi

echo "Hello, $1"
```

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/0cd2279179c237509a4d16a41cdf5201464e2859/2026/day-17/task3.png)

2. Create `args_demo.sh` that:
   - Prints total number of arguments (`$#`)
   - Prints all arguments (`$@`)
   - Prints the script name (`$0`)
     
```bash
#!/bin/bash

echo "Total number of arguments is $#"
echo "All arguments are $@"
echo "Script name is $0"
```
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/0cd2279179c237509a4d16a41cdf5201464e2859/2026/day-17/task3a.png)

---

### Task 4: Install Packages via Script
1. Create `install_packages.sh` that:
   - Defines a list of packages: `nginx`, `curl`, `wget`
   - Loops through the list
   - Checks if each package is installed (use `dpkg -s` or `rpm -q`)
   - Installs it if missing, skips if already present
   - Prints status for each package

```bash
#!/bin/bash

package=("nginx" "curl" "dnsutils")
for pack in "${package[@]}"; do
        if dpkg -s "$pack" &>/dev/null; then
                echo "$pack already installed"
        else
                sudo apt-get install $pack -y
        fi
done
```

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/0cd2279179c237509a4d16a41cdf5201464e2859/2026/day-17/task4.png)

---

### Task 5: Error Handling
1. Create `safe_script.sh` that:
   - Uses `set -e` at the top (exit on error)
   - Tries to create a directory `/tmp/devops-test`
   - Tries to navigate into it
   - Creates a file inside
   - Uses `||` operator to print an error if any step fails

```bash
#!/bin/bash

set -e

mkdir /tmp/devops-test1 || echo "Directory already exists"
cd /tmp/devops-test1 || echo "Unable to enter directory"
touch file.txt || echo "File creation failed"
```

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/0cd2279179c237509a4d16a41cdf5201464e2859/2026/day-17/task5.png)

---
2. Modify your `install_packages.sh` to check if the script is being run as root — exit with a message if not.
```bash
#!/bin/bash

if [[ "$EUID" -ne 0 ]]; then
        echo "Please run as a ROOT USER"
        exit 1
fi

package=("nginx" "curl" "dnsutils")
for pack in "${package[@]}"; do
        if dpkg -s "$pack" &>/dev/null; then
                echo "$pack already installed"
        else
                sudo apt-get install $pack -y
        fi
done
```

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/0cd2279179c237509a4d16a41cdf5201464e2859/2026/day-17/task5a.png)

---

##  What you learned (3 key points)
- Difference between for and while loops
- Using command-line arguments
- Automating package installation
- Importance of root checks
- Basic error handling

## Challenges Faced
- How to check if a package is already installed (`dpkg -s`)
- Understanding `$EUID` and why root checks matter in scripts

---
