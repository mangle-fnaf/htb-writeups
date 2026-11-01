# HTB Starting Point - Synced

## Overview

Synced is a very easy Linux machine that introduces rsync, a file synchronization and transfer utility commonly used for backups and file sharing.

The goal is to identify the exposed rsync service, enumerate available modules, access a publicly available share, and retrieve the flag.

---

## Skills Practised

- Enumeration
- Service Discovery
- Rsync
- Share Enumeration
- File Retrieval
- Linux Fundamentals

---

## Environment

All commands in this writeup were performed using the Hack The Box AttackBox PowerShell environment.

---

## Enumeration

I started by running an Nmap scan against the target machine to identify any exposed services.

### Command

```powershell
nmap -sV <TARGET_IP>
```

### What it does

- `nmap` scans a target for open ports and services.
- `-sV` attempts to identify the service running on each open port.

### Why I used it

Before attempting any interaction with the target, I wanted to understand what services were available.

### Result

```text
873/tcp open rsync (protocol version 31)
```

The scan revealed an rsync service running on port 873.

### Key Finding

Port 873 is the default port used by rsync, making it the primary service to investigate further.

> Add screenshot: nmap-scan.png

---

## Share Enumeration

After identifying rsync, I wanted to see whether any modules were publicly available.

### Command

```powershell
rsync <TARGET_IP>::
```

### What it does

Connects to the rsync service and lists available modules.

### Why I used it

I needed to determine whether any accessible shares were exposed.

### Result

```text
public
```

A module named `public` was available.

> Add screenshot: share-enumeration.png

---

## Exploring the Share

After discovering the public module, I inspected its contents.

### Command

```powershell
rsync <TARGET_IP>::public
```

### What it does

Lists files contained within the specified rsync module.

### Why I used it

I wanted to identify any interesting files stored inside the share.

### Result

```text
flag.txt
```

A file named `flag.txt` was present.

> Add screenshot: file-discovery.png

---

## Flag Retrieval

Since the flag file was accessible, I attempted to download it.

### Command

```powershell
rsync <TARGET_IP>::public/flag.txt .
```

### What it does

Downloads the remote file into the current working directory.

### Why I used it

The file needed to be copied locally before it could be viewed.

### Result

The file was downloaded successfully.

---

### Command

```powershell
ls
```

### What it does

Lists files in the current directory.

### Why I used it

To verify that `flag.txt` had been downloaded successfully.

### Result

```text
flag.txt
```

The file was now present locally.

---

### Command

```powershell
cat flag.txt
```

### What it does

Displays the contents of a text file.

### Why I used it

To read the contents of the downloaded flag file.

### Result

The flag was displayed and submitted successfully.

---

## Challenges & Learning Moments

While retrieving the flag, I initially ran:

```powershell
rsync <TARGET_IP>::public/flag.txt
```

This displayed information about the remote file but did not actually download it.

I then attempted to verify the download using:

```powershell
ls
```

and noticed that `flag.txt` was not present.

After reviewing the command syntax, I realised rsync requires a destination path when transferring files. Adding a trailing `.` solved the issue:

```powershell
rsync <TARGET_IP>::public/flag.txt .
```

The file was then downloaded successfully.

### What I Learned

Small syntax differences can completely change how a command behaves. Reading command output carefully helped me identify the mistake and correct it.

---

## Commands Used

```powershell
nmap -sV <TARGET_IP>

rsync <TARGET_IP>::

rsync <TARGET_IP>::public

rsync <TARGET_IP>::public/flag.txt .

ls

cat flag.txt
```

---

## What I Learned

- How to identify rsync during service enumeration.
- How to list available rsync modules.
- How to inspect the contents of a share.
- How to transfer files using rsync.
- The importance of understanding command syntax.
- The risks associated with publicly accessible file shares.

---

## Key Takeaway

Synced demonstrated how publicly accessible file-sharing services can expose sensitive information without requiring exploitation. By identifying the rsync service, enumerating available modules, and retrieving an exposed file, I was able to complete the room using only basic enumeration techniques. This reinforced the importance of reviewing share permissions and restricting anonymous access wherever possible.