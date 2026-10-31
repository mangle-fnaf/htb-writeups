# HTB Starting Point - Preignition

## Overview

Preignition is a very easy Linux machine focused on web enumeration, directory busting, and the risks associated with default credentials.

The goal is to identify the exposed web service, discover hidden content, and gain access to the administration panel.

---

## Skills Practised

- Enumeration
- Service Discovery
- HTTP
- Web Enumeration
- Gobuster
- Default Credentials
- Web Application Testing

---

## Environment

All commands in this writeup were performed using the Hack The Box AttackBox PowerShell environment.

---

## Enumeration

I started by identifying the services running on the target machine.

### Command

```powershell
nmap -sV <TARGET_IP>
```

### What it does

- `nmap` scans a target for open ports and services.
- `-sV` attempts to identify the service running on each open port.

### Why I used it

I wanted to understand what services were exposed before interacting with the target.

### Result

```text
80/tcp open http
```

The scan revealed an HTTP service running on port 80.

---

## Website Investigation

Since a web server was running, I opened the target IP address in a browser.

### Observation

The target displayed the default nginx welcome page.

This suggested that either the web application was not fully configured or that hidden content existed elsewhere on the server.

> Add screenshot: website-homepage.png

---

## Directory Enumeration

Because the homepage didn't reveal any obvious functionality, I used Gobuster to search for hidden files and directories.

### Command

```powershell
gobuster dir -w /usr/share/wordlists/dirb/common.txt -u http://<TARGET_IP>
```

### What it does

- Uses a wordlist to test common files and directories.
- Requests each path from the web server.
- Reports any pages that exist.

### Why I used it

The default nginx page contained no useful links, so I needed to discover hidden content manually.

### Result

```text
/admin.php
```

A hidden administrative page was discovered.

> Add screenshot: gobuster-results.png

---

## Challenges

During enumeration I made a few mistakes while learning how Gobuster works.

### Incorrect URL Formatting

I initially entered:

```powershell
-u http:// 10.129.x.x
```

which caused Gobuster to fail because the URL contained a space.

### Incorrect Wordlist Path

I also attempted to use:

```powershell
/usr/share/wordlists/common.txt
```

which did not exist on the system.

After investigating the available wordlists, I corrected the path to:

```powershell
/usr/share/wordlists/dirb/common.txt
```

### Lesson Learned

Small syntax mistakes can cause tools to fail completely. Carefully reading the error messages helped me identify and fix the issues.

---

## Accessing the Admin Panel

After discovering the hidden page, I navigated to:

```text
http://<TARGET_IP>/admin.php
```

### Why I used it

Administrative pages often contain privileged functionality and are common targets during web application assessments.

### Result

A login page was displayed.

> Add screenshot: admin-login.png

---

## Authentication

Since the page appeared to be using a default nginx installation, I tested common default credentials.

### Credentials Used

```text
Username: admin
Password: admin
```

### Why I tried it

Default credentials are a common weakness in newly deployed or poorly configured systems.

### Result

Authentication was successful.

---

## Flag Retrieval

After successfully logging in, the application displayed the flag.

The flag was retrieved and submitted successfully.

---

## Commands Used

```powershell
nmap -sV <TARGET_IP>

gobuster dir -w /usr/share/wordlists/dirb/common.txt -u http://<TARGET_IP>
```

---

## What I Learned

- How to identify web applications during enumeration.
- How to investigate web servers manually.
- How to use Gobuster for directory discovery.
- The importance of paying attention to command syntax.
- The risks associated with default credentials.
- How hidden administrative pages can expose sensitive functionality.

---

## Key Takeaway

Preignition demonstrated that insecure configuration can be just as dangerous as a software vulnerability. By enumerating the web server, discovering a hidden administration page, and testing default credentials, I was able to gain access without exploiting any technical flaw. This reinforced the importance of proper web application configuration, directory security, and credential management.