# HTB Starting Point - Fawn

## Overview

Fawn is a very easy Linux machine that introduces FTP (File Transfer Protocol) and the risks associated with anonymous authentication.

The goal is to identify the exposed service, connect to it, and retrieve the flag from the target system.

---

## Skills Practised

- Enumeration
- Service Discovery
- FTP
- Anonymous Authentication
- File Transfer
- Basic Linux Commands

---

## Enumeration

The first thing I did was run an Nmap scan against the target machine to identify any exposed services.

### Command

```bash
nmap -sV <TARGET_IP>
```

### What it does

- `nmap` scans a target for open ports and services.
- `-sV` attempts to identify the service running on each open port.

### Why I used it

Before attempting any kind of exploitation, I wanted to understand what services were available and identify a potential entry point.

### Result

```text
21/tcp open ftp
```

The scan revealed that an FTP service was running on port 21.

---

## Exploitation

Since FTP was exposed, I connected to the service to see whether authentication was required.

### Command

```bash
ftp <TARGET_IP>
```

### What it does

Connects to a remote FTP server.

### Why I used it

The Nmap scan identified FTP as the exposed service, so it made sense to investigate it further.

### Result

The server prompted me for a username.

---

### Authentication

```text
anonymous
```

### Why I tried it

Anonymous authentication is commonly tested when enumerating FTP services because some servers allow public access without requiring a legitimate user account.

### Result

I was able to authenticate successfully and access the FTP server.

---

## Flag Retrieval

After successfully authenticating, I wanted to see what files were available on the server.

### Command

```bash
ls
```

### What it does

Lists files and directories in the current FTP directory.

### Why I used it

To identify any interesting files that might contain sensitive information or the flag.

### Result

```text
flag.txt
```

A file named `flag.txt` was available on the server.

---

### Command

```bash
get flag.txt
```

### What it does

Downloads a file from the FTP server to my local machine.

### Why I used it

Since the flag was stored remotely, I needed to download the file before viewing its contents.

### Result

The file was successfully transferred to my machine.

---

### Command

```bash
bye
```

### What it does

Closes the FTP connection.

### Why I used it

After downloading the file, the FTP session was no longer required.

---

### Command

```bash
cat flag.txt
```

### What it does

Displays the contents of a file.

### Why I used it

To view the contents of the downloaded flag file.

### Result

The flag was displayed and submitted successfully.

---

## Commands Used

```bash
nmap -sV <TARGET_IP>

ftp <TARGET_IP>

anonymous

ls

get flag.txt

bye

cat flag.txt
```

---

## What I Learned

- How to identify services using Nmap.
- How FTP works and why it is commonly used for file transfers.
- The risks associated with anonymous FTP authentication.
- How to transfer files from a remote server.
- The importance of checking for weak or misconfigured services during enumeration.

---

## Challenges

This machine introduced me to FTP and anonymous authentication. It reinforced the importance of investigating exposed services because a simple configuration mistake can provide direct access to sensitive files.

---

## Key Takeaway

Fawn showed that attackers do not always need to exploit software vulnerabilities. In this case, anonymous access to an FTP server provided direct access to files stored on the system. This highlighted the importance of securing services properly and restricting access to sensitive data.