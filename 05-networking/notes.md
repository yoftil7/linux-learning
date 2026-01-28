# Linux Networking — Ports, Services & Binding (05-networking)

## 1. What Is a Port?

A **port** is a logical endpoint used by the OS to route network traffic to the correct process.

* IP address → identifies the host
* Port number → identifies the service/process

Example:

```
0.0.0.0:53
```

Means:

* Port `53`
* Listening on all network interfaces

---

## 2. Services & Listening Processes

A **service** is any process that opens a port and waits for connections.

You can inspect listening services using:

```
ss -tulnp
```

Flags:

* `t` → TCP
* `u` → UDP
* `l` → listening
* `n` → numeric output
* `p` → process info

This command answers:

* Which ports are open
* Which protocol (TCP/UDP)
* Which process owns them
* Which IP they are bound to

---

## 3. TCP vs UDP (At a Glance)

### TCP

* Connection-oriented
* Reliable
* Uses states: LISTEN, ESTABLISHED, etc.
* Used by: HTTP, HTTPS, SSH, SMTP

### UDP

* Connectionless
* No delivery guarantees
* Faster, lightweight
* Used by: DNS, DHCP, streaming

Example TCP listener:

```
nc -l 53
```

---

## 4. Binding: Where a Service Listens (Critical Concept)

### 127.0.0.1 (localhost)

* Only accessible from the same machine
* Not reachable from the network
* Safer by default

Example:

```
nc -l 127.0.0.1 9000
```

```
127.0.0.1:9000
```

Used for:

* Databases
* Internal services
* Development tools

---

### 0.0.0.0 (All Interfaces)

* Listens on all available interfaces
* Reachable from:

  * LAN
  * Docker bridge
  * Public IP (if exposed)

Example:

```
nc -l 9001
```

```
0.0.0.0:9001
```

This **increases attack surface** if not firewalled.

---

## 5. Security Implication of Binding

Binding determines **who can reach a service**.

* `127.0.0.1` → local access only (safe)
* `0.0.0.0` → network access (potentially dangerous)

A service bound to `0.0.0.0` must be:

* Intended to be public, or
* Protected by a firewall

---

## 6. Hands-On Lab Summary

Observed with `ss -tulnp`:

```
127.0.0.1:9000  → nc (local only)
0.0.0.0:9001    → nc (all interfaces)
```

Conclusion:

> Allowing access to a process on `0.0.0.0` can be dangerous if exposed to real external IPs.
> Binding to `127.0.0.1` limits access to the local machine and is safer by default.

---

## 7. Professional Rules of Thumb

* Not all open ports are bad — **uncontrolled exposure is**
* Always check **what IP a service is bound to**
* Default to `127.0.0.1` unless network access is required
* Firewalls protect ports — binding decides exposure
* `ss -tulnp` is a core diagnostic tool

---

## Next section -- 

# Linux Networking — Firewalls & Traffic Control (05-networking)

## 1. What Is a Firewall?

A **firewall** controls **which network traffic is allowed or denied**.

Key distinction:

* **Binding** decides *where* a service listens
* **Firewall** decides *who* can reach it

Even if a port is open and bound to `0.0.0.0`, a firewall can still block access.

---

## 2. Traffic Flow Model (Mental Model)

Incoming packet flow:

1. Packet reaches the machine
2. Firewall evaluates rules
3. If allowed → sent to listening port/process
4. If denied → dropped or rejected

No firewall rule = default behavior applies.

---

## 3. iptables / nftables (Low-Level Reality)

Under the hood, Linux uses:

* **nftables** (modern)
* **iptables** (legacy, still common)

Most admins do **not** manage these directly anymore.

Instead, they use:

* `ufw` (Ubuntu / Debian)
* `firewalld` (RHEL / CentOS / Fedora)

---

## 4. UFW (Uncomplicated Firewall)

UFW is a **frontend** to iptables/nftables.

### Check status

```
ufw status
```

### Enable firewall

```
ufw enable
```

### Disable firewall

```
ufw disable
```

---

## 5. Allowing and Blocking Ports

### Allow a port (TCP)

```
ufw allow 22/tcp
```

### Allow a port (UDP)

```
ufw allow 53/udp
```

### Deny a port

```
ufw deny 9001
```

Rules are evaluated top-down.

---

## 6. Allow From Specific IP (Very Important)

Example:

```
ufw allow from 192.168.1.10 to any port 22
```

Meaning:

* Only that IP can SSH
* Everyone else is blocked

This is **real security**, not theater.

---

## 7. Firewall vs Binding (Critical Comparison)

| Scenario                                      | Result                   |
| --------------------------------------------- | ------------------------ |
| Service bound to `127.0.0.1`                  | Not reachable externally |
| Service bound to `0.0.0.0`, firewall blocks   | Not reachable            |
| Service bound to `0.0.0.0`, firewall allows   | Reachable                |
| Service bound to `127.0.0.1`, firewall allows | Still NOT reachable      |

👉 Binding happens **before** firewall relevance.

---

## 8. Docker & Firewalls (Reality Check)

Docker:

* Automatically manipulates iptables
* Can expose ports even if you forget

Example:

```
docker run -p 8080:80 nginx
```

This:

* Opens port 8080
* Bypasses some UFW rules unless configured properly

Rule:

> Always verify with `ss -tulnp` after Docker changes.

---

## 9. Common Mistakes

* Thinking “service running” = “publicly accessible”
* Forgetting firewall exists
* Exposing `0.0.0.0` without protection
* Assuming cloud security groups = local firewall

Defense is **layered**, not single-point.

---

## 10. Interview Triggers

**Q:** Firewall vs binding?
**A:** Binding controls listening interfaces; firewall controls traffic access.

**Q:** Can a firewall protect a badly bound service?
**A:** Yes, but correct binding reduces risk first.

**Q:** Why bind databases to localhost?
**A:** They are not meant to be accessed directly over the network.

---

## 11. Professional Rules of Thumb

* Bind narrowly, firewall broadly
* Default deny > allow explicitly
* Verify exposure with `ss`, not assumptions
* Firewalls are policy, binding is architecture
* Docker changes rules — always recheck

---

End of firewall & traffic control notes.


# DNS — Full Resolution Model (05-networking)

## What DNS Is

DNS (Domain Name System) translates **human-readable names** into **IP addresses**.

Example:

```
google.com → 8.8.8.8
```

DNS is **distributed, hierarchical, and cached**.

---

## DNS Components

### 1. Stub Resolver

* Runs on your machine
* Sends DNS queries
* Uses `/etc/resolv.conf`

---

### 2. Recursive Resolver

* Usually ISP or public DNS (8.8.8.8, 1.1.1.1)
* Performs full resolution on your behalf
* Caches responses

---

### 3. Root Servers (.)

* Top of DNS hierarchy
* Know TLD servers
* Never return IP addresses

---

### 4. TLD Servers (.com, .org, .net)

* Know authoritative servers
* Delegate responsibility

---

### 5. Authoritative Servers

* Source of truth
* Store A, AAAA, MX, CNAME records
* Controlled by domain owner

---

## DNS Resolution Flow

1. Client asks recursive resolver
2. Resolver queries root servers
3. Root delegates to TLD
4. TLD delegates to authoritative server
5. Authoritative server returns record
6. Resolver caches and responds to client

---

## Common DNS Record Types

* **A** → IPv4 address
* **AAAA** → IPv6 address
* **CNAME** → Alias
* **MX** → Mail servers
* **NS** → Name servers
* **TXT** → Verification / metadata

---

## TTL (Time To Live)

TTL controls:

* How long records are cached
* Load on DNS infrastructure
* Speed of failover

Low TTL = fast changes
High TTL = efficiency & stability

---

## Why DNS Sometimes “Fails”

* Resolver cache poisoning
* Expired or misconfigured records
* Authoritative server unreachable
* Firewall blocking UDP/TCP 53
* Geo-DNS inconsistencies

---

## Key Commands

```
dig google.com
dig google.com +trace
nslookup google.com
```

Each provides different visibility levels.

---

## Professional Insight

> DNS is not a lookup — it is a **delegation system with caching and authority**.

---


# DNS Deep Dive — dig +trace (05-networking)

## Purpose of `dig +trace`

`dig +trace` performs DNS resolution manually, starting from the root servers, bypassing local recursive resolvers.

It simulates how a recursive DNS resolver works.

---

## DNS Resolution Flow Observed

1. **Root servers (.)**

   * Do not know domain IPs
   * Delegate to TLD servers
   * Very high TTL

2. **TLD servers (.com)**

   * Do not know host IPs
   * Delegate to authoritative servers
   * Medium TTL

3. **Authoritative servers (google.com)**

   * Source of truth
   * Return A / AAAA records
   * Lower TTL for flexibility

4. **Final Answer**

   * IP address returned
   * DNS resolution complete

---

## Key Concepts Reinforced

* DNS is hierarchical and distributed
* Delegation happens at every layer
* Caching is controlled by TTL
* Clients normally use recursive resolvers
* `dig +trace` bypasses recursion for visibility

---

## Why This Matters

* Debug DNS failures
* Detect DNS hijacking
* Understand CDN routing
* Verify authoritative responses
* Analyze TTL and caching behavior

---

## Professional Insight

> Root and TLD servers never resolve hostnames — they only delegate.

---

End of DNS trace notes.


# Traceroute — Path Discovery & TTL Mechanics (05-networking)

## What Traceroute Does

`traceroute` discovers the **network path** packets take from your machine to a destination.

It works by manipulating the **TTL (Time To Live)** field in IP packets.

---

## Core Mechanism

1. Traceroute sends packets with **TTL = 1**
2. First router decrements TTL → reaches 0
3. Router drops the packet and replies with:

   * **ICMP Time Exceeded**
4. Traceroute records that router’s IP
5. TTL is incremented (2, 3, 4…) until destination is reached

Each hop = one router along the path.

---

## Why `* * *` Appears

A hop shows `* * *` when:

* Router drops TTL-expired packets silently
* ICMP replies are firewalled
* Rate-limiting is applied
* Router does not respond to probes

This does **not** mean the path is broken.

---

## Traceroute Modes

### Default (UDP-based)

```
traceroute google.com
```

* Sends UDP packets to high ports
* Expects ICMP Time Exceeded
* Often blocked by firewalls

---

### TCP-based (Real-world traffic)

```
traceroute -T -p 443 google.com
```

* Uses TCP SYN packets
* Mimics HTTPS traffic
* More likely to pass firewalls
* Common in production debugging

---

## TTL Direction Clarification

* TTL is decremented **on every hop**
* TTL applies to **each packet independently**
* ICMP replies are **new packets** with their own TTL
* You only see TTL behavior via replies

TTL decreases on **both forward and return paths**, but only replies are visible.

---

## Practical Insights

* Traceroute shows **path**, not performance guarantees
* Missing hops ≠ unreachable destination
* Different protocols reveal different paths
* TCP traceroute is closer to real application behavior

---

End of traceroute notes.

