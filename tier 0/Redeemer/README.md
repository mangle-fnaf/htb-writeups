# HTB Starting Point - Redeemer

## Overview

Redeemer is a very easy Linux machine that introduces Redis, an in-memory key-value database commonly used for caching and fast data storage.

The goal is to identify the exposed service, connect to the Redis database, enumerate its contents, and retrieve the flag.

---

## Skills Practised

- Enumeration
- Service Discovery
- Redis
- Database Enumeration
- Data Retrieval
- Basic Redis Commands

---

## Environment

All commands in this writeup were performed using the Hack The Box AttackBox PowerShell environment.

---

## Enumeration

I began by running a standard Nmap scan to identify any open ports and services.

### Command

```powershell
nmap -sV <TARGET_IP>
```

### What it does

- `nmap` scans a target for open ports and services.
- `-sV` attempts to identify the service running on each open port.

### Why I used it

Before interacting with the target, I wanted to identify any exposed services and determine possible attack vectors.

### Result

```text
All 1000 scanned ports on the target were closed.
```

The default scan did not identify any services.

### What I Learned

The default Nmap scan only checks the most common 1000 ports. If a service is running on a less common port, it may not appear in the results.

---

## Additional Enumeration

The room hint suggested scanning all ports, so I expanded the scan.

### Command

```powershell
nmap -p- --min-rate 5000 <TARGET_IP>
```

### What it does

- `-p-` scans all 65,535 TCP ports.
- `--min-rate 5000` increases the scan speed by specifying a minimum packet rate.

### Why I used it

The initial scan returned no results, so a full port scan was necessary to identify services running outside the default top 1000 ports.

### Result

An open port was discovered and further investigation revealed a Redis service.

---

## Exploitation

After identifying Redis, I connected to the service using the Redis command-line client.

### Command

```powershell
redis-cli -h <TARGET_IP>
```

### What it does

Connects to a remote Redis server.

### Why I used it

Redis was the only exposed service, so it became the primary target for enumeration.

### Result

I successfully connected to the Redis instance.

---

## Database Enumeration

Once connected, I wanted to verify the connection and inspect the contents of the database.

### Command

```text
ping
```

### What it does

Sends a test request to the Redis server.

### Why I used it

To confirm that the connection was working correctly.

### Result

```text
PONG
```

The server responded successfully.

---

### Command

```text
dbsize
```

### What it does

Displays the number of keys stored in the current database.

### Why I used it

I wanted to determine whether any data was stored in the database.

### Result

The database contained several keys.

---

### Command

```text
keys *
```

### What it does

Lists all keys stored in the current database.

### Why I used it

To identify potentially interesting values stored within Redis.

### Result

Several keys were discovered, including a key named:

```text
flag
```

This appeared to be the most relevant item for further investigation.

---

## Flag Retrieval

After identifying the flag key, I retrieved its value.

### Command

```text
get flag
```

### What it does

Returns the value stored inside the specified key.

### Why I used it

The key name strongly suggested it contained the challenge flag.

### Result

The flag value was displayed and submitted successfully.

---

## Commands Used

```powershell
nmap -sV <TARGET_IP>

nmap -p- --min-rate 5000 <TARGET_IP>

redis-cli -h <TARGET_IP>

ping

dbsize

keys *

get flag
```

---

## What I Learned

- The default Nmap scan does not always reveal every open service.
- How to perform a full TCP port scan.
- How Redis stores information using key-value pairs.
- How to connect to a Redis server using `redis-cli`.
- How to enumerate a Redis database and retrieve stored values.
- The importance of properly securing database services.

---

## Challenges

The initial Nmap scan returned no useful results, which reinforced the importance of adapting enumeration techniques when the first approach does not provide enough information. Performing a full port scan ultimately revealed the service required to complete the machine.

---

## Key Takeaway

Redeemer demonstrated that sensitive information can be exposed through poorly secured database services. By expanding my enumeration beyond the default Nmap scan and interacting directly with Redis, I was able to discover and retrieve the required information without exploiting a software vulnerability.