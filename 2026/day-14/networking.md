# Day 14 – Networking Basics & Troubleshooting

## Quick Concepts

### OSI Model vs TCP/IP Model

| OSI Layer         | Description                                   | TCP/IP Equivalent |
| ----------------- | --------------------------------------------- | ----------------- |
| L7 – Application  | User-facing network services (HTTP, DNS, FTP) | Application       |
| L6 – Presentation | Data formatting, encryption                   | Application       |
| L5 – Session      | Session management between hosts              | Application       |
| L4 – Transport    | Reliable/fast delivery (TCP/UDP)              | Transport         |
| L3 – Network      | Logical addressing & routing (IP)             | Internet          |
| L2 – Data Link    | MAC addressing, switching                     | Link              |
| L1 – Physical     | Electrical signals, cables                    | Link              |

**Summary**

* **OSI** has **7 layers**, mainly used for learning and conceptual troubleshooting.
* **TCP/IP** has **4 layers**, used in real-world networking implementations.

---

### Where Common Protocols Sit

| Protocol     | Layer                | Explanation                                             |
| ------------ | -------------------- | ------------------------------------------------------- |
| IP           | Internet (OSI L3)    | Handles addressing and routing packets between networks |
| TCP          | Transport (OSI L4)   | Reliable connection-based communication                 |
| UDP          | Transport (OSI L4)   | Fast connectionless communication                       |
| HTTP / HTTPS | Application (OSI L7) | Web communication protocol                              |
| DNS          | Application (OSI L7) | Resolves domain names to IP addresses                   |

---

### Real Example

Example request

```
curl https://example.com
```

How it works in the stack

* Application → HTTP/HTTPS request
* Transport → TCP connection (port 443)
* Internet → IP routing to destination server
* Link → Data sent through network interface

**In simple words**

`curl https://example.com` = HTTP request (Application) over TCP (Transport) over IP (Internet) over Network Interface (Link)

---

# Hands-on Networking Checklist

Target host used for testing

```
google.com
```

---

## 1. Identity – Check Local IP

Command

```bash
hostname -I
```

Example Output

```
192.168.1.25
```

Observation

* My machine has a **private IP address** in the `192.168.x.x` range.
* This indicates it is behind a **router/NAT network**.

Alternative command

```bash
ip addr show
```

---

## 2. Reachability – Ping Target

Command

```bash
ping -c 4 google.com
```

Example Output

```
64 bytes from 142.250.183.14: icmp_seq=1 ttl=116 time=23.4 ms
64 bytes from 142.250.183.14: icmp_seq=2 ttl=116 time=24.1 ms
```

Observation

* Average latency ~ **23 ms**
* **0% packet loss**
* Network connectivity to the host is healthy.

If packet loss occurs, it may indicate

* Network congestion
* Firewall blocking ICMP
* Routing problems

---

## 3. Path Analysis – Traceroute

Command

```bash
traceroute google.com
```

Example Output (shortened)

```
1  192.168.1.1
2  10.10.0.1
3  isp-gateway.net
4  google-backbone.net
```

Observation

* Shows the **network path** between my machine and Google.
* Each hop represents a **router**.
* High latency or `* * *` may indicate routing issues or blocked ICMP.

Alternative command

```bash
tracepath google.com
```

---

## 4. Ports & Listening Services

Command

```bash
ss -tulpn
```

Example Output

```
tcp LISTEN 0 128 0.0.0.0:22 users:(("sshd",pid=752))
tcp LISTEN 0 128 0.0.0.0:80 users:(("nginx",pid=911))
```

Observation

Example listening services

* **SSH running on port 22**
* **Web server running on port 80**

Listening ports mean the system is **accepting incoming connections**.

---

## 5. Name Resolution – DNS

Command

```bash
dig google.com
```

Example Output

```
google.com.  300 IN A 142.250.183.14
```

Observation

* Domain `google.com` resolves to **142.250.183.14**
* DNS converts **human-readable names into IP addresses**.

Alternative command

```bash
nslookup google.com
```

---

## 6. HTTP Check

Command

```bash
curl -I https://google.com
```

Example Output

```
HTTP/2 200
server: gws
content-type: text/html
```

Observation

* Status code **200** means request was successful.

Common status codes

* **200** → Success
* **404** → Page not found
* **500** → Server error
* **301/302** → Redirect

---

## 7. Connections Snapshot

Command

```bash
netstat -an | head
```

Example Output

```
tcp 0 0 127.0.0.1:22 0.0.0.0:* LISTEN
tcp 0 0 192.168.1.25:54322 142.250.183.14:443 ESTABLISHED
```

Observation

* **LISTEN** → services waiting for connections
* **ESTABLISHED** → active connections

Example rough count

* LISTEN: 3
* ESTABLISHED: 1

---

# Mini Task – Port Probe & Interpretation

First identify a listening port

```
ss -tulpn
```

Example found

```
SSH listening on port 22
```

Test connectivity

```bash
nc -zv localhost 22
```

Example Output

```
Connection to localhost 22 port [tcp/ssh] succeeded!
```

Observation

* Port **22 is reachable**
* SSH service is active.

If connection fails

Next checks

Verify service is running

```bash
systemctl status ssh
```

Check firewall rules

```bash
sudo ufw status
```

---

# Reflection

## 1. Which command gives the fastest signal when something is broken?

**ping**

Reason

* Quickly verifies **basic network connectivity**
* If ping fails, the issue is likely **network or routing related**.

---

## 2. What layer would you inspect next?

### If DNS fails

Check **Application Layer**

Commands

```
dig
nslookup
```

Also verify

* `/etc/resolv.conf`
* DNS server availability

---

### If HTTP 500 appears

Issue is at the **Application Layer**

Possible causes

* Web server failure
* Backend application error

Check

* Web server logs
* Application service status

---

## 3. Two Follow-up Checks in a Real Incident

1. Verify service status

```bash
systemctl status nginx
```

2. Check firewall rules

```bash
sudo ufw status
```

Other useful checks

* `ss -tulpn`
* `journalctl -xe`
* `curl -v`

---

# Key Takeaway

Basic troubleshooting flow

```
DNS → Connectivity → Routing → Port → Application
```

Typical command order during debugging

```
ping
dig
traceroute
ss / netstat
curl
```

This layered approach helps isolate network problems quickly.
