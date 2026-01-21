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

End of current networking notes.

