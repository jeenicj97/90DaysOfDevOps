# Day 12 – Breather & Revision (Days 01–11)

> **Goal:** Consolidate everything from Days 01–11. No new concepts — just retention, reflection, and a few quick hands-on reruns.

---

## 🧠 Mindset & Plan Check-In (Day 01 Revisit)

### Original goals — are they still right?
- [ ] Review your Day 01 learning plan now and ask: *What have I actually covered? What still feels weak?*
- [ ] Tweak timeline if needed — it's normal to slow down on permissions or process concepts.

**Suggested tweaks to consider:**
- If `chmod` numbers still feel fuzzy → dedicate 10 min on Day 13 to numeric vs symbolic mode practice.
- If `journalctl` flags felt overwhelming → bookmark 2–3 flags you'll actually use (`-u`, `-n`, `--since`).
- If user/group ownership clicked well → move ahead with confidence to scripting or networking days.

---

## ⚙️ Processes & Services Review (Days 04–05)

Rerun at least **2 of these commands** and jot what you observed:

```bash
# List running processes — snapshot view
ps aux

# Filter for a specific process (e.g., sshd, nginx, cron)
ps aux | grep sshd

# Check if a service is active
systemctl status ssh

# View recent logs for a service
journalctl -u ssh -n 20

# Follow live logs (Ctrl+C to stop)
journalctl -u ssh -f

# List all running services
systemctl list-units --type=service --state=running
```

### 📝 What to jot down:
- Is the service `active (running)` or `inactive (dead)`?
- Any `failed` units in the list?
- What's the most recent log entry for your chosen service?

**Why this matters:** In a real incident, `systemctl status` + `journalctl` are your first two moves before touching anything else.

---

## 📁 File Skills Practice (Days 06–11)

Pick **3 quick operations** and run them. Suggested set:

```bash
# 1. Append text to a file (non-destructive write)
echo "revision note - $(date)" >> ~/day12-notes.txt

# 2. Check what you just wrote
cat ~/day12-notes.txt

# 3. Create a directory
mkdir -p ~/revision/test-dir

# 4. Copy a file into it
cp ~/day12-notes.txt ~/revision/test-dir/

# 5. Check permissions and ownership
ls -l ~/revision/test-dir/

# 6. Change permissions — remove write for group and others
chmod go-w ~/revision/test-dir/day12-notes.txt

# 7. Verify the change
ls -l ~/revision/test-dir/day12-notes.txt
```

### chmod Quick Reference (often confused):

| Mode | Meaning |
|------|---------|
| `chmod 755 file` | owner=rwx, group=rx, others=rx |
| `chmod 644 file` | owner=rw, group=r, others=r |
| `chmod 700 file` | owner=rwx only — no one else |
| `chmod go-w file` | remove write from group + others (symbolic) |
| `chmod +x script.sh` | add execute for everyone |

**Numeric reminder:** `r=4, w=2, x=1` → add them up per user class (owner / group / others).

---

## 📋 Cheat Sheet Refresh (Day 03)

Skim your Day 03 command list. Highlight **5 you'd reach for first in an incident:**

### Recommended top-5 incident commands:

```bash
# 1. Who's logged in right now?
who
w

# 2. What's the system load / memory situation?
top
htop          # if installed
free -h

# 3. What's eating disk space?
df -h
du -sh /var/log/*

# 4. What processes are running / what's using CPU?
ps aux --sort=-%cpu | head -20

# 5. What happened recently in logs?
journalctl -n 50 --no-pager
tail -n 50 /var/log/syslog
```

**Why these 5:** They give you a full picture — who, what process, what resource, what log — without touching anything yet. Observe first, act second.

---

## 👤 User & Group Sanity Check (Days 09 & 11)

Recreate one small scenario and verify it. Example:

```bash
# 1. Create a test user (requires sudo)
sudo useradd -m -s /bin/bash testuser

# 2. Verify the user exists
id testuser
grep testuser /etc/passwd

# 3. Create a file as root and change ownership to testuser
sudo touch /tmp/testfile.txt
sudo chown testuser:testuser /tmp/testfile.txt

# 4. Verify ownership
ls -l /tmp/testfile.txt
# Expected: -rw-r--r-- 1 testuser testuser ...

# 5. Clean up
sudo userdel -r testuser
rm /tmp/testfile.txt
```

**What to look for in `id testuser`:**
```
uid=1001(testuser) gid=1001(testuser) groups=1001(testuser)
```

---

## ✅ Mini Self-Check

### 1. Which 3 commands save you the most time right now, and why?

Write your own honest answer here. Example answers:

- **`ls -lh`** — instantly see permissions, owner, and size in one line; saves digging.
- **`grep <pattern> <file>`** — find exactly what I need in a config or log without scrolling.
- **`systemctl status <service>`** — tells me in 3 seconds if a service is running and shows the last few log lines.

> ✏️ *Your answer:*
> 1.
> 2.
> 3.

---

### 2. How do you check if a service is healthy?

The exact commands, in order:

```bash
# Step 1 — Is it running?
systemctl status <service-name>

# Step 2 — Any errors in the logs?
journalctl -u <service-name> -n 30

# Step 3 — Is it listening on the expected port? (bonus)
ss -tlnp | grep <port>
# or
netstat -tlnp | grep <port>
```

> ✏️ *Your answer (fill in your own words):*

---

### 3. How do you safely change ownership and permissions without breaking access?

**Rule of thumb:** Always check current state with `ls -l` before changing anything.

```bash
# Safe example — change ownership of a web directory to www-data
ls -l /var/www/html/            # check first
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/
ls -l /var/www/html/            # verify after
```

**Why `-R` carefully:** Recursive (`-R`) applies to everything inside. Never run `chmod -R 777` — it removes all security. Use `755` for dirs, `644` for files as a safe baseline.

> ✏️ *Your example command:*

---

### 4. What will you focus on improving in the next 3 days?

Suggestions based on common sticking points:

- [ ] `chmod` numeric mode — still shaky? → drill 5 examples by hand.
- [ ] `journalctl` filtering flags (`--since`, `--until`, `-p err`) → practice on real logs.
- [ ] `find` command (if covered) — powerful but syntax trips people up.
- [ ] Shell scripting basics — if you're ready to level up.

> ✏️ *Your focus areas:*
> 1.
> 2.
> 3.

---

## 🗂️ Key Takeaways — Days 01–11 in One Page

| Day | Topic | Remember This |
|-----|-------|---------------|
| 01 | Learning plan | Goals, timeline, motivation |
| 02 | Linux orientation | Everything is a file; CLI is the real interface |
| 03 | Core commands | `ls`, `cd`, `pwd`, `man`, `grep`, `cat`, `less` |
| 04 | Processes | `ps aux`, `kill`, `top` — find & manage processes |
| 05 | Services | `systemctl start/stop/enable/status` |
| 06 | File operations | `cp`, `mv`, `rm`, `mkdir`, `touch` |
| 07 | Text & editors | `nano`/`vim` basics, `echo`, `cat`, redirection `>` `>>` |
| 08 | Permissions | `chmod`, `chown` — numeric + symbolic modes |
| 09 | Users | `useradd`, `passwd`, `userdel`, `id`, `/etc/passwd` |
| 10 | Groups | `groupadd`, `usermod -aG`, `groups`, `/etc/group` |
| 11 | Ownership scenarios | `chown user:group`, `ls -l` verification |

---

## 🏁 Done? Mark your checkpoint:

- [ ] Skimmed notes from each day
- [ ] Updated Day 01 plan if needed
- [ ] Reran 2 process/service commands
- [ ] Practiced 3 file operations
- [ ] Refreshed cheat sheet top-5
- [ ] Recreated one user/ownership scenario
- [ ] Wrote self-check answers
