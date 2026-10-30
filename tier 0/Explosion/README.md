# HTB Starting Point - Explosion

## Overview

Explosion is a very easy Windows machine that introduces Remote Desktop Protocol (RDP) enumeration and remote access.

The goal is to identify the exposed services on the target, establish a remote desktop connection, and retrieve the flag.

---

## Skills Practised

- Enumeration
- Service Discovery
- RDP
- Remote Access
- Windows Basics
- Flag Retrieval

---

## Environment

All commands in this writeup were performed using the Hack The Box AttackBox PowerShell environment.

---

## Enumeration

I began by running an Nmap scan to identify any open ports and services on the target machine.

### Command

```powershell
nmap -sV <TARGET_IP>
```

### What it does

- `nmap` scans a target for open ports and services.
- `-sV` attempts to identify the service running on each open port.

### Why I used it

Before attempting any further interaction with the target, I wanted to identify available services and determine possible attack vectors.

### Result

```text
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
5985/tcp open  http
```

The scan indicated that the target was a Windows machine with several exposed services, including RDP on port 3389.

### Key Finding

The most interesting service was:

```text
3389/tcp open ms-wbt-server
```

which indicated that Remote Desktop Protocol (RDP) was available.

---

## Exploitation

Since RDP was exposed, I attempted to establish a remote desktop session.

### Command

```powershell
xfreerdp /v:<TARGET_IP> /u:Administrator /d:"" /cert:ignore
```

### What it does

- `xfreerdp` is an open-source RDP client.
- `/v:` specifies the target host.
- `/u:` specifies the username.
- `/d:""` uses a blank domain.
- `/cert:ignore` ignores certificate warnings.

### Why I used it

The Nmap scan showed that RDP was exposed, so I attempted to connect using the Administrator account.

### Result

A remote desktop session was established successfully.

---

## Flag Retrieval

After connecting via RDP, I was presented with the Windows desktop.

### What I observed

A file containing the flag was visible from within the remote desktop session.

### Why this was important

The challenge focused on gaining remote access through RDP. Once connected, retrieving the flag was straightforward because it was already accessible from the desktop environment.

### Result

The flag was successfully retrieved and submitted.

---

## Commands Used

```powershell
nmap -sV <TARGET_IP>

xfreerdp /v:<TARGET_IP> /u:Administrator /d:"" /cert:ignore
```

---

## What I Learned

- How to identify RDP during service enumeration.
- How to interpret common Windows services discovered during an Nmap scan.
- How to connect to a remote Windows machine using `xfreerdp`.
- How exposed remote access services can provide direct access to a system.
- The importance of securing remote administration services.

---

## Challenges

This machine introduced me to Remote Desktop Protocol and showed how exposed remote access services can become an attack vector. It reinforced the importance of carefully reviewing scan results and identifying which service is most relevant to investigate further.

---

## Key Takeaway

Explosion demonstrated how service enumeration can quickly reveal a path into a target system. By identifying an exposed RDP service and establishing a remote desktop session, I was able to access the machine and retrieve the flag. This highlighted the importance of securing remote administration services and limiting unnecessary exposure to the internet.