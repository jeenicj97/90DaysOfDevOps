# Linux File System Hierarchy + Troubleshooting Notes

---

## Part 1 — Linux File System Hierarchy

### 1. `/` — Root Directory

**Purpose**
The root directory is the starting point of the entire Linux file system. All files and directories exist under `/`.

```bash
ls -l /
```

Example observations:
```
home/
etc/
var/
usr/
```

> I would use this when I need to navigate the entire Linux file system or troubleshoot disk usage.

---

### 2. `/home`

**Purpose**
Contains personal directories for normal users. Each user gets their own folder here.

```bash
ls -l /home
```

Example observations:
```
jeeni/
ubuntu/
```

> I would use this when I need to access user files, scripts, downloads, or application data.

---

### 3. `/root`

**Purpose**
Home directory of the root (admin) user. Different from `/`.

```bash
ls -l /root
```

Example observations:
```
.bashrc
scripts/
```

> I would use this when I am troubleshooting as root or storing admin-only scripts.

---

### 4. `/etc`

**Purpose**
Stores system-wide configuration files. Very important for DevOps and server management.

```bash
ls -l /etc
```

Example observations:
```
hostname
hosts
ssh/
```

> I would use this when I need to modify service configs, network settings, or system behavior.

---

### 5. `/var/log`

**Purpose**
Contains application and system log files. Critical directory for troubleshooting.

```bash
ls -l /var/log
```

Example observations:
```
syslog
auth.log
journal/
```

> I would use this when a service fails, a server crashes, or I need error logs.

---

### 6. `/tmp`

**Purpose**
Stores temporary files created by users and applications. Files may get deleted automatically after reboot.

```bash
ls -l /tmp
```

Example observations:
```
temp123/
cachefile.tmp
```

> I would use this when applications need temporary storage or while debugging scripts.

---

### 7. `/bin`

**Purpose**
Contains essential Linux command binaries needed for boot and basic operation.

```bash
ls -l /bin
```

Example observations:
```
bash
ls
cat
```

> I would use this when I need core Linux commands during troubleshooting.

---

### 8. `/usr/bin`

**Purpose**
Contains most user-level command binaries and applications.

```bash
ls -l /usr/bin | head
```

Example observations:
```
python3
vim
git
```

> I would use this when I need tools installed for users or development work.

---

### 9. `/opt`

**Purpose**
Stores optional or third-party applications.

```bash
ls -l /opt
```

Example observations:
```
google/
custom-app/
```

> I would use this when installing vendor software or custom enterprise applications.

---

## Hands-On Tasks

### Find Largest Log Files

```bash
du -sh /var/log/* 2>/dev/null | sort -h | tail -5
```

> Helps identify logs consuming disk space.

### View Hostname Config

```bash
cat /etc/hostname
```

> Shows the server hostname.

### Check Home Directory

```bash
ls -la ~
```

> Shows hidden files, permissions, and user environment configs.

---

## Part 2 — Scenario-Based Practice

### Scenario 1 — Service Not Starting

```bash
# Step 1: Check service status
systemctl status myapp

# Step 2: Read latest logs
journalctl -u myapp -n 50

# Step 3: Check if service is enabled on boot
systemctl is-enabled myapp

# Step 4: Restart the service
systemctl restart myapp
```

| Step | Command | Why |
|------|---------|-----|
| 1 | `systemctl status myapp` | Checks whether the service is active, failed, or stopped |
| 2 | `journalctl -u myapp -n 50` | Reads the latest logs to identify startup errors |
| 3 | `systemctl is-enabled myapp` | Checks whether the service starts automatically after reboot |
| 4 | `systemctl restart myapp` | Attempts to restart the service after investigation |

> **What I learned:** Always check status first, then logs, then boot settings.

---

### Scenario 2 — High CPU Usage

```bash
# Step 1: Live CPU and memory overview
top

# Step 2: List top CPU-consuming processes
ps aux --sort=-%cpu | head -10

# Step 3: Inspect the problematic process
ps -p <PID> -f

# Step 4: Stop a runaway process if necessary
kill -9 <PID>
```

| Step | Command | Why |
|------|---------|-----|
| 1 | `top` | Shows live CPU and memory usage |
| 2 | `ps aux --sort=-%cpu \| head -10` | Lists top CPU-consuming processes |
| 3 | `ps -p <PID> -f` | Gets detailed info about the problematic process |
| 4 | `kill -9 <PID>` | Stops a runaway process if necessary |

> **What I learned:** Always identify the process before killing it.

---

### Scenario 3 — Finding Service Logs

```bash
# Step 1: Check service status
systemctl status docker

# Step 2: Read latest 50 log lines
journalctl -u docker -n 50

# Step 3: Follow logs in real-time
journalctl -u docker -f

# Step 4: Check logs from a specific time range
journalctl -u docker --since "1 hour ago"
```

| Step | Command | Why |
|------|---------|-----|
| 1 | `systemctl status docker` | Checks if the docker service is running properly |
| 2 | `journalctl -u docker -n 50` | Reads the latest 50 log lines for docker |
| 3 | `journalctl -u docker -f` | Follows logs in real-time like `tail -f` |
| 4 | `journalctl -u docker --since "1 hour ago"` | Checks logs from a specific time range |

> **What I learned:** systemd services usually store logs in journald.

---

### Scenario 4 — File Permission Issue

```bash
# Step 1: Check current permissions
ls -l /home/user/backup.sh

# Step 2: Add execute permission
chmod +x /home/user/backup.sh

# Step 3: Verify the change
ls -l /home/user/backup.sh

# Step 4: Run the script
./backup.sh
```

| Step | Command | Why |
|------|---------|-----|
| 1 | `ls -l /home/user/backup.sh` | Checks whether execute permission exists |
| 2 | `chmod +x /home/user/backup.sh` | Adds execute permission to the script |
| 3 | `ls -l /home/user/backup.sh` | Confirms the file is now executable |
| 4 | `./backup.sh` | Tests whether the issue is resolved |

---

## Final Notes — Key DevOps Takeaways

| Area | Why It Matters |
|------|----------------|
| `/etc` | Config troubleshooting |
| `/var/log` | Service debugging |
| `systemctl` | Service management |
| `journalctl` | Log investigation |
| `top` + `ps` | Performance troubleshooting |
| `chmod` | Permission fixes |
