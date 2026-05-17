# Linux Fundamentals

## What is Linux?

- An open-source operating system
- Comes in different distributions (distros): Ubuntu, CentOS, RHEL, Debian, etc.

---

## Architecture (top to bottom)

```
Terminal (Utility)
    ↓
Shell (CLI — communicates with kernel)
    ↓
Kernel (heart of Linux — written in C)
    ↓
Hardware
```

When you run a command like `mkdir` in the terminal:
1. Shell sends it to the kernel
2. Kernel converts it to binary (0s and 1s)
3. Kernel checks with hardware and executes

---

## Boot process

```
Power ON
  → BIOS (Basic Input/Output System — lives on motherboard)
  → Bootloader
  → Kernel starts
  → PID 1: systemd (system daemon)
  → Services start: docker, k8s, ssh, etc.
```

- **systemd** is a background process — it is always PID 1
- Everything in Linux is a process

---

## Shell types

| Shell | Path |
|-------|------|
| bash  | `/usr/bin/bash` |
| sh    | `/usr/bin/sh` |
| zsh   | `/usr/bin/zsh` |

> Each shell has its own binary path. `/usr/bin/bash` is specifically bash's location — other shells live at their own paths.

---

## Key filesystem paths

| Path | Purpose |
|------|---------|
| `/` | Root directory — top of the entire filesystem |
| `/boot/vmlinuz` | Where the kernel image file is stored (loaded into RAM at boot) |
| `/bin` | All core Linux commands (`ls`, `mkdir`, etc.) |
| `/var/log` | System and service logs |
| `~` | Home directory of current user |
| `/usr/bin/bash` | Location of bash shell |

### File type indicators (from `ls -l`)

| Symbol | Meaning |
|--------|---------|
| `d` | Directory |
| `l` | Symbolic link |
| `-` | Regular file |

---

## Ways to access Linux

- Docker Desktop
- WSL (Windows Subsystem for Linux) — runs real Linux on Windows
- AWS EC2 (cloud Linux server)
- VirtualBox
- VMware
- Vagrant by HashiCorp (Vagrant is a tool made by HashiCorp)
- Git Bash — gives a bash shell on Windows but does NOT run real Linux; it only emulates some Linux commands

---

## Process states

| State | Meaning |
|-------|---------|
| **Running** | Actively using the CPU right now |
| **Sleeping** | Waiting for I/O, a timer, or a signal |
| **Stopped** | Paused — via `Ctrl+Z` or `SIGSTOP` |
| **Zombie** | Finished but parent hasn't read exit code yet |
| **Uninterruptible** | Deep kernel sleep — cannot be killed (D state) |

---

## 5 daily commands (beginner friendly)

---

### 1. `pwd` — where am I?
Stands for **Print Working Directory**. Shows which folder you are currently in.
```bash
pwd
# output: /home/yourname
```

---

### 2. `ls` — what's in this folder?
Lists all files and folders in the current directory.
```bash
ls        # basic list
ls -l     # detailed list (shows file type, size, permissions)
ls -la    # includes hidden files (files starting with .)
```

---

### 3. `cd` — move between folders
Stands for **Change Directory**. How you navigate around Linux.
```bash
cd /var/log       # go to the logs folder
cd ~              # go back to your home directory
cd ..             # go one folder up
```

---

### 4. `systemctl status <service>` — is this service running?
Check the current state of any service (like docker, nginx, ssh).
```bash
systemctl status docker
systemctl status ssh
```
Look for `Active: active (running)` — that means it's healthy.
If you see `failed` — the service has crashed.

---

### 5. `cat` — read a file
Prints the contents of a file to the screen. Very useful for reading logs or config files.
```bash
cat /var/log/syslog       # read system logs
cat /etc/os-release       # see which Linux distro you are on
```

---

*Source: Class notes — Linux fundamentals lecture*
