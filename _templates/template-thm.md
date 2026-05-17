---
title: "THM - RoomName"
date: 2026-05-16 14:00:00 +0200
categories: [Writeup, TryHackMe]
tags: [linux, web, beginner]
description: "One-line summary of the room."
image:
  path: /assets/img/posts/thm-roomname.png
  alt: "TryHackMe - RoomName"
---

## Overview

| Field | Info |
|-------|------|
| OS | Linux |
| Difficulty | Easy |
| Type | Free / Subscriber |
| Room | [RoomName](https://tryhackme.com/room/roomname) |

Brief description of what the room covers.

---

## Task 1 — Setup / Deploy

```bash
# Connect to VPN
sudo openvpn ~/thm.ovpn

# Target
export IP=10.10.X.X
```

---

## Task 2 — Recon

```bash
nmap -sC -sV $IP
```

```
PORT   STATE SERVICE
xx/tcp open  ...
```

---

## Task 3 — Exploitation

Step-by-step of the exploitation.

```bash
# commands
```

> Tip or note about this step.
{: .prompt-tip }

---

## Task N — Flags / Answers

| Question | Answer |
|----------|--------|
| What is the user flag? | `flag{...}` |
| What is the root flag? | `flag{...}` |

---

## Key Takeaways

- Lesson 1
- Lesson 2
