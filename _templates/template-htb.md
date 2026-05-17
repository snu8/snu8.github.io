---
title: "HTB - MachineName"
date: 2026-05-16 14:00:00 +0200
categories: [Writeup, HackTheBox]
tags: [linux, web, easy]
description: "One-line summary of the machine."
image:
  path: /assets/img/posts/htb-machinename.png
  alt: "HackTheBox - MachineName"
---

## Overview

| Field | Info |
|-------|------|
| OS | Linux |
| Difficulty | Easy |
| Release | 2026-01-01 |
| Retired | Yes |

Brief description of what the machine is about and key concepts covered.

---

## Recon

### Nmap

```bash
nmap -sC -sV -oN nmap/initial.txt 10.10.10.X
```

```
PORT   STATE SERVICE VERSION
xx/tcp open  http    Apache ...
```

> Ports notables : ...
{: .prompt-info }

### Web Enumeration

```bash
gobuster dir -u http://10.10.10.X -w /usr/share/wordlists/dirb/common.txt
```

---

## Foothold

What vulnerability was found and how it was exploited.

```bash
# exploit command
```

> Note or warning about this step.
{: .prompt-warning }

---

## Lateral Movement

*(si applicable)*

---

## Privilege Escalation

What misconfig / vulnerability allowed privesc.

```bash
sudo -l
```

```
(root) NOPASSWD: /usr/bin/something
```

Explanation of the privesc path.

---

## Flags

| Flag | Location |
|------|----------|
| user.txt | `/home/user/user.txt` |
| root.txt | `/root/root.txt` |

---

## Key Takeaways

- Lesson 1
- Lesson 2
- Lesson 3
