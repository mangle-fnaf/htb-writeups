# HTB Starting Point - Mongod

## Overview

Mongod is a very easy Linux machine that introduces MongoDB enumeration and the security risks associated with unauthenticated database access.

The goal is to identify the exposed MongoDB service, connect to the database, enumerate its contents, and retrieve the flag.

---

## Skills Practised

- Enumeration
- Service Discovery
- MongoDB
- Database Enumeration
- NoSQL Databases
- Data Retrieval
- Troubleshooting

---

## Environment

All commands in this writeup were performed using the Hack The Box AttackBox PowerShell environment.

---

## Enumeration

I began by running a standard Nmap scan against the target.

### Command

```powershell
nmap -sV <TARGET_IP>
```

### What it does

- `nmap` scans a target for open ports and services.
- `-sV` attempts to identify service versions.

### Why I used it

I wanted to identify any exposed services before investigating further.

### Result

```text
22/tcp open ssh
```

The initial scan only identified SSH running on port 22.

### What I Learned

The default Nmap scan only checks the most common 1000 ports. Important services can be missed if they are running on less common ports.

> Add screenshot: initial-scan.png

---

## Additional Enumeration

Since the initial scan did not reveal much information, I performed a full TCP port scan.

### Command

```powershell
nmap -p- --min-rate=1000 -sV <TARGET_IP>
```

### What it does

- `-p-` scans all 65,535 TCP ports.
- `--min-rate=1000` speeds up the scan.
- `-sV` attempts to identify service versions.

### Why I used it

I wanted to identify services running on non-standard ports that the default scan would have missed.

### Result

```text
22/tcp    open ssh
27017/tcp open mongodb
```

MongoDB was discovered on port 27017.

> Add screenshot: full-port-scan.png

---

## Exploitation

After identifying MongoDB, I connected to the database using the MongoDB shell.

### Command

```powershell
./mongosh mongodb://<TARGET_IP>:27017
```

### What it does

Connects to the remote MongoDB server.

### Why I used it

MongoDB was the newly discovered service and appeared to be the intended attack vector.

### Result

A connection to the MongoDB instance was established successfully.

> Add screenshot: mongo-connection.png

---

## Database Enumeration

Once connected, I began investigating the available databases.

### Command

```javascript
show dbs
```

### What it does

Displays all databases hosted on the MongoDB server.

### Why I used it

I wanted to identify databases that might contain useful information.

### Result

A database named:

```text
sensitive_information
```

stood out as the most interesting target.

> Add screenshot: database-enumeration.png

---

### Command

```javascript
use sensitive_information
```

### What it does

Changes the active database.

### Why I used it

The database name suggested it contained valuable information.

### Result

Successfully switched to the database.

---

### Command

```javascript
show collections
```

### What it does

Lists collections stored in the selected database.

### Why I used it

I needed to identify where the data was stored.

### Result

```text
flag
```

A collection named `flag` was discovered.

> Add screenshot: collections.png

---

## Flag Retrieval

After identifying the collection, I retrieved the stored document.

### Command

```javascript
db.flag.find()
```

### What it does

Displays all documents inside the collection.

### Why I used it

The collection name suggested the flag was stored there.

### Result

The flag was displayed and submitted successfully.

---

## Challenges & Learning Moments

### Initial Nmap Scan Missed MongoDB

My first scan only identified SSH running on port 22. This initially made it seem as though the target had very little exposed functionality.

By performing a full TCP port scan, I discovered MongoDB running on port 27017. This reinforced the importance of adapting enumeration techniques when the initial results are limited.

### Incorrect Nmap Syntax

At one point, I attempted to scan using incorrect syntax which caused Nmap to interpret the input incorrectly.

Reading the error messages carefully helped me understand how Nmap expects ports and hosts to be specified.

### Incorrect IP Address

I accidentally omitted the final digit of the target IP address while testing commands.

This highlighted the importance of double-checking command inputs before troubleshooting more complex issues.

### MongoDB Commands in the Wrong Shell

I initially attempted to run MongoDB commands such as:

```javascript
show dbs
```

from a PowerShell prompt.

This taught me the importance of recognising which environment I was currently working in and understanding the difference between PowerShell commands and MongoDB shell commands.

### Missing MongoDB Shell

I also encountered issues because MongoDB shell tools were not immediately available in the environment.

Researching the walkthrough and reviewing the required tooling helped me understand the dependencies needed to interact with database services.

---

## Commands Used

```powershell
nmap -sV <TARGET_IP>

nmap -p- --min-rate=1000 -sV <TARGET_IP>

./mongosh mongodb://<TARGET_IP>:27017
```

```javascript
show dbs

use sensitive_information

show collections

db.flag.find()
```

---

## What I Learned

- The default Nmap scan does not identify every service.
- How to perform a full TCP port scan.
- How to identify a MongoDB database service.
- How MongoDB structures data using databases, collections, and documents.
- How to connect to a MongoDB instance.
- How to enumerate databases and collections.
- The risks associated with unauthenticated database access.
- The importance of troubleshooting and understanding error messages.

---

## Key Takeaway

Mongod demonstrated how exposed databases can lead to sensitive information disclosure without requiring a software exploit. By expanding my enumeration beyond a standard Nmap scan and exploring the MongoDB instance, I was able to retrieve the flag through a simple configuration weakness rather than a technical vulnerability.