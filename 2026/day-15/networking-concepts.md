# Day 15 – Networking Concepts: DNS, IP, Subnets & Ports

## Task 1: DNS – How Names Become IPs

When you type `google.com` in a browser, the browser asks the operating system to resolve the domain name.
The OS queries a DNS resolver, which checks its cache or contacts DNS servers.
DNS servers translate the domain name into an IP address.
The browser then connects to that IP address to load the website.

### DNS Record Types

| Record | Description                                            |
| ------ | ------------------------------------------------------ |
| A      | Maps a domain name to an IPv4 address                  |
| AAAA   | Maps a domain name to an IPv6 address                  |
| CNAME  | Alias that points one domain name to another           |
| MX     | Specifies mail servers responsible for receiving email |
| NS     | Indicates the authoritative DNS servers for a domain   |

### Command

```bash
dig google.com
```

Example output:

```
;; ANSWER SECTION:
google.com.    299    IN    A    142.250.190.14
```

A Record: `142.250.190.14`
TTL: `299`

---

# Task 2: IP Addressing

### What is an IPv4 Address?

An IPv4 address is a 32-bit numerical label assigned to devices in a network.
It is written as four numbers separated by dots (for example `192.168.1.10`).
Each number ranges from **0–255**, representing 8 bits.

Example structure:

```
192.168.1.10
|   |   |   |
Octets (8 bits each)
```

### Public vs Private IP

| Type       | Description                                                                | Example      |
| ---------- | -------------------------------------------------------------------------- | ------------ |
| Public IP  | Globally unique address reachable from the internet                        | 8.8.8.8      |
| Private IP | Used inside internal networks and not directly reachable from the internet | 192.168.1.10 |

### Private IP Ranges

```
10.0.0.0 – 10.255.255.255
172.16.0.0 – 172.31.255.255
192.168.0.0 – 192.168.255.255
```

### Command

```bash
ip addr show
```

Example output:

```
inet 192.168.1.25/24 brd 192.168.1.255 scope global dynamic
```

Private IP detected: `192.168.1.25`

---

# Task 3: CIDR & Subnetting

### What does `/24` mean in `192.168.1.0/24`?

`/24` means that the first **24 bits** represent the network portion of the address and the remaining **8 bits are used for hosts**.

### Host Calculations

Formula:

```
Total IPs = 2^(host bits)
Usable hosts = Total IPs - 2
```

(Network address and broadcast address cannot be used)

### Why Do We Subnet?

Subnetting divides a large network into smaller logical networks.
This improves network organization, increases security, and reduces unnecessary broadcast traffic.

### CIDR Table

| CIDR | Subnet Mask     | Total IPs | Usable Hosts |
| ---- | --------------- | --------- | ------------ |
| /24  | 255.255.255.0   | 256       | 254          |
| /16  | 255.255.0.0     | 65536     | 65534        |
| /28  | 255.255.255.240 | 16        | 14           |

---

# Task 4: Ports – The Doors to Services

### What is a Port?

A port is a logical communication endpoint used by applications to send and receive data over a network.
Ports allow multiple services to run on the same IP address.

### Common Ports

| Port  | Service |
| ----- | ------- |
| 22    | SSH     |
| 80    | HTTP    |
| 443   | HTTPS   |
| 53    | DNS     |
| 3306  | MySQL   |
| 6379  | Redis   |
| 27017 | MongoDB |

### Command

```bash
ss -tulpn
```

Example output:

```
tcp LISTEN 0 128 0.0.0.0:22 users:(("sshd",pid=1001))
tcp LISTEN 0 128 0.0.0.0:80 users:(("nginx",pid=1150))
```

Matched services:

| Port | Service      |
| ---- | ------------ |
| 22   | SSH (sshd)   |
| 80   | HTTP (nginx) |

---

# Task 5: Putting It Together

### You run `curl http://myapp.com:8080` — what networking concepts are involved?

First, DNS resolves `myapp.com` into an IP address.
Then the request connects to that IP using port **8080**, which is where the application server is listening.
Networking protocols like TCP route the request to the correct host and service.

### Your app can't reach a database at `10.0.1.50:3306` — what would you check first?

First check if the host is reachable using tools like `ping` or `traceroute`.
Next verify that the database service is running and listening on port **3306**.
Finally check firewall rules, security groups, or network policies that may block the connection.

---

# What I Learned (Key Points)

1. DNS converts human-readable domain names into IP addresses so computers can communicate.
2. CIDR notation defines network size and determines how many hosts can exist in a subnet.
3. Ports allow multiple services to run on a single machine by assigning each service its own communication endpoint.
