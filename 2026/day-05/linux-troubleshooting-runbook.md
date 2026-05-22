# Linux Troubleshooting Runbook

**Target service/process:** `docker`

---

## Environment Basics

### Check Kernel & OS Details

```bash
uname -a
```

> Confirms architecture & kernel version before troubleshooting deeper issues.

### Check Distribution Information

```bash
cat /etc/os-release
```

> Useful for checking compatibility issues or package-related bugs.

---

## Filesystem Sanity Checks

### Create Temporary Working Directory

```bash
mkdir -p /tmp/runbook-demo
```

> Temporary troubleshooting workspace created; also confirms filesystem is writable.

### Copy & Verify File Operations

```bash
cp /etc/hosts /tmp/runbook-demo/hosts-copy && ls -l /tmp/runbook-demo
```

> File copy succeeded — permissions & storage access appear normal.

---

## Process & Resource Monitoring

### Snapshot Docker Process Resource Usage

```bash
ps -o pid,pcpu,pmem,comm -C dockerd
```

> Docker daemon is active with low usage — no immediate resource exhaustion detected.

### Check System Memory

```bash
free -h
```

> Healthy available memory — swap usage is minimal, indicating no memory pressure currently.

---

## Disk & IO

### Check Disk Usage

```bash
df -h
```

> Root filesystem has sufficient free space — no immediate disk exhaustion risk.

### Check Log Directory Size

```bash
du -sh /var/log
```

> Logs consume moderate storage — worth monitoring if logs are growing rapidly.

---

## Network

### Check Listening Ports

```bash
# Unix sockets
ss -xlpn | grep docker

# TCP/UDP ports
ss -tulpn
```

> Docker daemon is listening on expected port/interface — confirms service networking is active.

### Verify Service Connectivity

```bash
curl --unix-socket /var/run/docker.sock http://localhost/_ping
```

> **Note:** Docker does not expose its API over TCP port 2375 by default. It listens on a Unix socket (`/var/run/docker.sock`).

> Docker API endpoint responds successfully — service reachable locally without timeouts/errors.

---

## Logs

### Review Docker Service Logs

```bash
journalctl -u docker -n 50
```

> No critical errors in recent logs — service startup appears healthy.

### Review System Log Tail

```bash
tail -n 50 /var/log/syslog
```

> No kernel panic, OOM, or disk-related warnings observed — general system logs appear stable.

---

## Quick Findings

- Docker service is running normally.
- CPU and memory usage are low.
- Disk space is healthy with no filesystem concerns.
- Network connectivity to the Docker API is successful.
- No major errors detected in logs.
- System appears stable at the time of investigation.

---

## Escalation Steps (If This Worsens)

### 1. Restart Strategy

```bash
sudo systemctl restart docker
sudo systemctl start docker
```

> Restart docker service if containers stop responding or API becomes unavailable.

### 2. Increase Logging & Monitor Live Events

```bash
# Stream live Docker logs
journalctl -u docker -f

# Monitor container lifecycle events
docker events
```

> Monitor real-time logs and container lifecycle events for intermittent failures.

### 3. Advanced Tracing & Performance Tools

```bash
# Trace system calls on the Docker daemon
strace -p <dockerd-pid>

# Live process monitor
top

# IO monitoring
iotop
```

> Use tracing & performance tools if CPU spikes, hangs, or IO bottlenecks occur.
