# HTB Starting Point - Meow

## Overview

Meow is a very easy Linux machine that introduces basic enumeration and remote access using Telnet.

The goal is to identify the exposed service, gain access to the machine, and retrieve the flag.

---

## Skills Practised

- Enumeration
- Service Discovery
- Telnet
- Linux Basics
- File Enumeration

---

## Enumeration

The first thing I did was run an Nmap scan against the target machine to see what services were exposed.

### Command

```bash
nmap -sV <TARGET_IP>
```

### What it does

- `nmap` scans a target for open ports and services.
- `-sV` attempts to identify the service running on each open port.

### Why I used it

Before trying anything else, I wanted to understand what was running on the machine and identify possible attack vectors.

### Result

```text
23/tcp open telnet
```

The scan showed that Telnet was running on port 23.

---

## Exploitation

Since Telnet was exposed, I attempted to connect to the service.

### Command

```bash
telnet <TARGET_IP>
```

### What it does

Connects to a remote Telnet service.

### Why I used it

The Nmap scan identified Telnet as the only exposed service, so it made sense to investigate it further.

After connecting, the service prompted for a username.

### Authentication

```text
root
```

### Why I tried it

I tested the root account because it is the default administrator account on Linux systems and is often a good starting point during basic enumeration.

### Result

I successfully gained access to the machine.

---

## Flag Retrieval

Once I had access to the system, I wanted to see what files were available.

### Command

```bash
ls
```

### What it does

Lists files and directories in the current location.

### Why I used it

To look for anything interesting, including the flag file.

### Result

```text
flag.txt
```

A file called `flag.txt` was present.

---

### Command

```bash
cat flag.txt
```

### What it does

Displays the contents of a file.

### Why I used it

To read the contents of the flag file.

### Result

The flag was displayed and submitted successfully.

---

## Commands Used

```bash
nmap -sV <TARGET_IP>

telnet <TARGET_IP>

root

ls

cat flag.txt
```

---

## What I Learned

- How to identify services using Nmap.
- How Telnet works and why it is considered insecure.
- The importance of enumeration before attempting exploitation.
- How weak authentication can lead to full system compromise.
- Basic Linux file navigation and inspection.

---

## Challenges

This was my first time interacting with a machine through Telnet. The challenge reinforced the importance of identifying exposed services before attempting exploitation and showed how insecure remote access protocols can create unnecessary risk when they are not configured securely.

---

## Key Takeaway

This machine showed that not every compromise requires a complicated exploit. Simply identifying an exposed service and testing basic access was enough to gain access to the system. It also reinforced why Telnet is considered insecure and why administrative accounts should be properly protected.