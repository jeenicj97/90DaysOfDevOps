## Networking Fundamentals

### OSI Model vs TCP/IP Stack

**OSI Model (7 layers) — theoretical reference model:**
- L1 Physical — actual cables, WiFi signals, hardware
- L2 Data Link — MAC addresses, switches (how data moves on local network)
- L3 Network — IP addresses, routers (how data moves between networks)
- L4 Transport — TCP/UDP (how data is delivered reliably or fast)
- L5 Session — manages connections between two systems
- L6 Presentation — encryption, compression, data formatting
- L7 Application — HTTP, HTTPS, DNS, FTP (what the user/app sees)

**TCP/IP Stack (4 layers) — what actually runs in the real world:**
- Link — combines OSI L1+L2 (cables, MAC addresses, switches)
- Internet — OSI L3 (IP addresses, routing)
- Transport — OSI L4 (TCP/UDP)
- Application — OSI L5+L6+L7 (HTTP, DNS, SSH, FTP)

> OSI is the theory you study. TCP/IP is what Linux, 
> Docker, and Kubernetes actually use.

---

### Where Protocols Sit

| Protocol | Layer | What it does |
|---|---|---|
| IP | Internet (L3) | Addressing — gets packet to the right machine |
| TCP | Transport (L4) | Reliable delivery — guarantees data arrives in order |
| UDP | Transport (L4) | Fast delivery — no guarantee, used for DNS, video calls |
| HTTP/HTTPS | Application (L7) | Web requests — what your browser and curl speak |
| DNS | Application (L7) | Translates domain names to IP addresses |
| SSH | Application (L7) | Secure remote terminal access |

---

### Real Example

```
curl https://example.com
```
 
Walk through the stack (bottom → top):
 
1. **L1 Physical** — electrical/radio signals move bits over the wire/Wi-Fi
2. **L2 Data Link** — Ethernet frame gets your packet to the local gateway via MAC address
3. **L3 Network (IP)** — IP packet routed across the internet to `example.com`'s address
4. **L4 Transport (TCP)** — 3-way handshake (SYN → SYN-ACK → ACK) opens a reliable connection on port 443
5. **L5/L6 (TLS)** — TLS handshake negotiates encryption (this is HTTPS)
6. **L7 Application (HTTP)** — `curl` sends `GET / HTTP/1.1`, server sends back the HTML
---

## Hands-on Checklist (run these; add 1–2 line observations)
- **Identity:** `hostname -I` (or `ip addr show`) — note your IP.
  
    ![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/95f8efa8cdca48920229906456e5b230fee69217/2026/day-14/task1.1.png)
 
> The system IP address is 172.30.43.17

> This is the address assigned to the machine on the local network.
  
 
- **Reachability:** `ping <target>` — mention latency and packet loss.
  
    ![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/95f8efa8cdca48920229906456e5b230fee69217/2026/day-14/task1.2.png)
  
  > 41 packets transmitted, 40 received, 2.43902% packet loss, time 41375ms
    rtt min/avg/max/mdev = 5.472/11.546/69.705/12.285 ms
  
  > Connectivity to the target host is successful.
  
  > 1 packet loss observed and latency is acceptable.

- **Path:** `traceroute <target>` (or `tracepath`) — note any long hops/timeouts.
  
    ![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/95f8efa8cdca48920229906456e5b230fee69217/2026/day-14/task1.3.png)
  
  > Traffic passes through multiple hops before reaching the destination.
  
  > No significant delays or failures observed.
- **Ports:** `ss -tulpn` (or `netstat -tulpn`) — list one listening service and its port.
  
    ![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/95f8efa8cdca48920229906456e5b230fee69217/2026/day-14/task1.4.png)
  
  > SSH service is listening on port 22.
  
- **Name resolution:** `dig <domain>` or `nslookup <domain>` — record the resolved IP.
  
    ![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/95f8efa8cdca48920229906456e5b230fee69217/2026/day-14/task1.5.png)
  
  > DNS resolution is working correctly.
  
- **HTTP check:** `curl -I <http/https-url>` — note the HTTP status code.
  
    ![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/95f8efa8cdca48920229906456e5b230fee69217/2026/day-14/task1.6.png)
> The server responded successfully.

- **Connections snapshot:** `netstat -an | head` — count ESTABLISHED vs LISTEN (rough).
  
    ![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/95f8efa8cdca48920229906456e5b230fee69217/2026/day-14/task1.7.png)
  
> LISTEN connections observed: 70

> ESTABLISHED connections observed: 24
  ---
## Mini Task: Port Probe & Interpret
1) Identify one listening port from `ss -tulpn` (e.g., SSH on 22 or a local web app).  
2) From the same machine, test it: `nc -zv localhost <port>` (or `curl -I http://localhost:<port>`).  
3) Write one line: is it reachable? If not, what’s the next check? (e.g., service status, firewall).
   
     ![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/95f8efa8cdca48920229906456e5b230fee69217/2026/day-14/task2.png)
> Selected NGINX (Port 80)
> Port 80 is reachable locally.

### If Not Reachable
- Verify service status.
 > systemctl status ssh
- Verify firewall rules.
 > sudo ufw status
---

## Reflection (add to your markdown)
- Which command gives you the fastest signal when something is broken?
  > ping provides a quick indication of basic network connectivity.
  
- What layer (OSI/TCP-IP) would you inspect next if DNS fails? If HTTP 500 shows up?
  > Application Layer.
  
- Two follow-up checks you’d run in a real incident.
  > Check service status using: systemctl status <service>
  > Review logs using: journalctl -xe

  ---
  
