# Linux commands cheatsheet

---

## Process management

| Command | What it does |
|---------|-------------|
| `ps aux` | Show all running processes with PID, CPU %, memory %, and command name (`ps` alone shows very little — always use `aux`) |
| `top` | Live view of system usage — CPU, memory, all processes. Press `q` to quit |
| `htop` | Better visual version of `top`. Not installed by default — run `sudo apt install htop` first |
| `kill <PID>` | Politely ask a process to stop |
| `kill -9 <PID>` | Force-kill a process immediately — use when normal kill doesn't work |
| `systemctl start <service>` | Start a service now |
| `systemctl stop <service>` | Stop a running service |
| `systemctl restart <service>` | Stop then start — use after config changes |
| `systemctl status <service>` | Check if a service is running and see recent logs inline |
| `systemctl enable <service>` | Make a service start automatically on every boot |
| `systemctl list-units --failed` | Show every service that has crashed — first thing to check during an incident |
| `journalctl -u <service> -f` | Watch live logs for a service (the `-f` flag is important — without it the command dumps logs and exits) |
| `journalctl -u <service> -n 50` | Show last 50 lines of logs for a service |

> **Tip:** PID = Process ID. Every process gets a unique number. Use `ps aux` to find it, then use the PID with `kill`. PID 1 is always systemd.

---

## File system

| Command | What it does |
|---------|-------------|
| `ls -la` | Detailed list including hidden files — shows file type, permissions, size, and owner |
| `cat <file>` | Print entire file to screen — good for short config files or logs |
| `tail -n 10 file.txt` | Show last 10 lines of a file |
| `tail -f <file>` | Watch a file update live — most useful for log files. `Ctrl+C` to stop |
| `grep "error" file.txt` | Search a file for the word "error" — only shows matching lines |
| `grep -r "error" /var/log/` | Search recursively through an entire folder |
| `df -h` | Show disk usage for all drives in human-readable sizes (GB, MB) |
| `du -sh <folder>` | Show how much space a specific folder is using |
| `chmod 755 file.txt` | Change file permissions — 755 means owner can do everything, others can read and run |
| `chown user:group <file>` | Change who owns a file |
| `find / -name "file.txt"` | Search for a file by name across the whole system |

> **Key paths to remember:**
> - `/var/log` — all logs
> - `/etc` — all config files
> - `/bin` — Linux commands
> - `~` — your home directory shortcut

---

## Networking troubleshooting

| Command | What it does |
|---------|-------------|
| `ping <host>` | Check if a host is reachable — sends packets and waits for a reply. `Ctrl+C` to stop |
| `traceroute <host>` | Show every hop (router) your request passes through — useful to find where it gets stuck |
| `nslookup <host>` | DNS resolution — look up the IP address of a domain, checks if DNS is working |
| `dig <domain>` | More detailed DNS lookup — shows the full response from the DNS server |
| `ss -tulnp` | Show all open ports and which process is listening on each one (modern replacement for `netstat`) |
| `netstat -tulnp` | Same as `ss` — older command, still available on most systems |
| `curl <url>` | Make an HTTP request and print the response — test if an API or website is reachable |
| `curl -I <url>` | Show only the response headers — quickly see the status code (200, 404, 500) |
| `wget <url>` | Download a file from a URL and save it to disk |
| `ip a` | Show all network interfaces and their IP addresses |
| `ip r` | Show the routing table — how your machine decides where to send traffic |
| `ip link` | Show network interface status (up/down) |

> **Troubleshooting order:**
> 1. `ping` — can I reach the host at all?
> 2. `nslookup` — is DNS resolving correctly?
> 3. `curl` — is the app responding over HTTP?
> 4. `ss -tulnp` — is the port actually open and listening?
>
> This sequence covers 90% of network issues.

---

*Source: DevOps foundations — Linux commands lecture*
