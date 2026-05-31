# Day 15 – Networking Concepts: DNS, IP, Subnets & Ports

## Challenge Tasks

### Task 1: DNS – How Names Become IPs
1. Explain in 3–4 lines: what happens when you type `google.com` in a browser?
   * The browser asks a DNS server to find the IP address associated with google.com.
   * DNS resolves the domain name and returns an IP address.
   * The browser connects to that IP address using TCP/IP.
   * The web server responds with the requested webpage over HTTP/HTTPS.
     
3. What are these record types? Write one line each: `A`, `AAAA`, `CNAME`, `MX`, `NS`
   * `A`: Maps a domain name to an IPv4 address
   * `AAAA`: Maps a domain name to an IPv6 address
   * `CNAME`: Points one domain name to another(alias)
   * `MX`: Specifies mail servers responsible for email delivery
   * `NS`: Identifies authoritative name servers for a domain
     
4. Run: `dig google.com` — identify the A record and TTL from the output
   * `A record` : 142.250.143.102
   * `TTL(Time to Live)` : 77 seconds
  ```
;; ANSWER SECTION:
google.com.             77      IN      A       142.250.143.102
google.com.             77      IN      A       142.250.143.100
google.com.             77      IN      A       142.250.143.101
google.com.             77      IN      A       142.250.143.139
google.com.             77      IN      A       142.250.143.138
google.com.             77      IN      A       142.250.143.113
```

---

### Task 2: IP Addressing
1. What is an IPv4 address? How is it structured?
   * An IPv4 address is a 32-bit numerical address used to identify devices on a network. 
   * Divided into four octets (e.g., 192.168.1.10) ; Each octet = 8 bits, so total = 32 bits ; Each section can have a value between 0 and 255.
     
3. Difference between **public** and **private** IPs — give one example of each
   
| Type       | Description                    | Example       |
|------------|--------------------------------|---------------|
| Public IP  | Reachable over the Internet    | 8.8.8.8       |
| Private IP | Used inside local networks     | 192.168.1.10  |
   
5. What are the private IP ranges?
   - `10.0.0.0` - `10.255.255.255`
   - `172.16.0.0` - `172.31.255.255`
   - `192.168.0.0` - `192.168.255.255`
     
6. Run: `ip addr show` — identify which of your IPs are private

* `127.0.0.1/8` → Loopback address (special, used for local testing, not public).
* `10.255.255.254/32` → Falls in the `10.x.x.x` private range → Private IP.
* `172.30.43.17/20` → Falls in `172.16.x.x – 172.31.x.x` private range → Private IP
* `192.168.49.1/24` → Falls in `192.168.x.x` private range → Private IP.
  
```
jeenicj@DESKTOP-BG3MAVI:/mnt/c/Users/Jeeni$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet 10.255.255.254/32 brd 10.255.255.254 scope global lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:15:5d:7b:0a:20 brd ff:ff:ff:ff:ff:ff
    inet 172.30.43.17/20 brd 172.30.47.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::215:5dff:fe7b:a20/64 scope link
       valid_lft forever preferred_lft forever
3: br-75a722e60e3e: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 32:f0:60:d8:7c:7e brd ff:ff:ff:ff:ff:ff
    inet 192.168.49.1/24 brd 192.168.49.255 scope global br-75a722e60e3e
```
---

### Task 3: CIDR & Subnetting
1. What does `/24` mean in `192.168.1.0/24`?
   * It means the first 24 bits are reserved for the network portion, leaving 8 bits for hosts. So the subnet mask is 255.255.255.0, and it allows 256 total IPs (0–255 in        the last octet).
     
3. How many usable hosts in a `/24`? A `/16`? A `/28`?
   * /24 → 256 total IPs → 254 usable hosts
   * /16 → 65,536 total IPs → 65,534 usable hosts
   * /28 → 16 total IPs → 14 usable hosts
   
5. Explain in your own words: why do we subnet?
   * Organises networks into smaller, manageable sections.
   * Improves performance and security.
   * Reduces broadcast traffic.
   * Makes IP address allocation more efficient.
     
6. Quick exercise — fill in:
   
   | CIDR | Subnet Mask       | Total IPs | Usable Hosts |
   |------|-------------------|-----------|--------------|
   | /24  | 255.255.255.0     | 256       | 254          |
   | /16  | 255.255.0.0       | 65,536    | 65,534       |
   | /28  | 255.255.255.240   | 16        | 14           |

---

### Task 4: Ports – The Doors to Services
1. What is a port? Why do we need them?
   * A port is a logical endpoint that helps identify which service or application should handle incoming or outgoing network traffic on a device. Since multiple services        can run on the same IP, ports act like “doors” that direct traffic to the right process.
     
2. Document these common ports:

   | Port  | Protocol |
   |-------|----------|
   | 22    | SSH      |
   | 80    | HTTP     |
   | 443   | HTTPS    |
   | 53    | DNS      |
   | 3306  | MySQL    |
   | 6379  | Redis    |
   | 27017 | MongoDB  |

3. Run `ss -tulpn` — match at least 2 listening ports to their services

   * `0.0.0.0:22`: SSH service(port 22)
   *  `0.0.0.0:80`: HTTP web server (port 80)

```
tcp        LISTEN      0           4096                       [::]:22                       [::]:*
tcp        LISTEN      0           4096                          *:10250                       *:*
tcp        LISTEN      0           4096                          *:16443                       *:*
tcp        LISTEN      0           511                        [::]:80                       [::]:*
tcp        LISTEN      0           4096                          *:25000                       *:*
```
---

### Task 5: Putting It Together
Answer in 2–3 lines each:
- You run `curl http://myapp.com:8080` — what networking concepts from today are involved?
- Your app can't reach a database at `10.0.1.50:3306` — what would you check first?
  
     > Is the IP address reachable (ping or traceroute)?

     > Is port 3306 open and the database service running?

     > Are firewall rules or network ACLs blocking traffic?

     > Is the app using correct credentials and network configuration?

---

## What you learned (3 key points)

> DNS resolution translates domain names into IP addresses with records like A and TTL values.

> IP addressing and subnetting organize networks into public/private ranges and manage host counts efficiently.

> Ports and services act as entry points for applications, e.g., 22 for SSH and 3306 for MySQL.

---
