---
layout: single
title: '<span class="htb">Hack The Box - Machine - Writer</span>'
excerpt: "[MACHINE-DESCRIPTION-HERE]"
date: 2021-10-31
header:
  teaser: /assets/images/htb-machine-writer/htb-machine-writer_banner.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hachthebox
  - writeup
tags:
  - nmap
  - machine
  - sqlinjection 
  - ssh
  - writeup
toc: true
toc_label: "Content"
toc_sticky: true
show_time: true
---

<!-- BANNER -->
<a href="/assets/images/htb-machine-writer/htb-machine-writer_banner.png">
    <img src="/assets/images/htb-machine-writer/htb-machine-writer_banner.png" alt="Hack The Box - Machine - Writer">
</a>

# Overview
[MACHINE-DESCRIPTION-HERE]

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
echo '10.10.11.101 writer.htb' >> /etc/hosts
ping -c 4 writer.htb
mkdir -p ~/htb/writer.htb/{exploits,fuzz,http,nmap}
```

# Recon
## Scan with nmap
Check **all ports** that are **open**, detect the service and its version with `nmap`
```bash
nmap -sC -sV -p- -T4 --min-rate=1000 -v -oA all writer.htb
```
```nmap
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 98:20:b9:d0:52:1f:4e:10:3a:4a:93:7e:50:bc:b8:7d (RSA)
|   256 10:04:79:7a:29:74:db:28:f9:ff:af:68:df:f1:3f:34 (ECDSA)
|_  256 77:c4:86:9a:9f:33:4f:da:71:20:2c:e1:51:10:7e:8d (ED25519)
80/tcp  open  http        Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Story Bank | Writer.HTB
| http-methods: 
|_  Supported Methods: GET HEAD OPTIONS
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: 15m09s
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| nbstat: NetBIOS name: WRITER, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| Names:
|   WRITER<00>           Flags: <unique><active>
|   WRITER<03>           Flags: <unique><active>
|   WRITER<20>           Flags: <unique><active>
|   \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
|   WORKGROUP<00>        Flags: <group><active>
|   WORKGROUP<1d>        Flags: <unique><active>
|_  WORKGROUP<1e>        Flags: <group><active>
| smb2-time: 
|   date: 2021-10-31T09:58:48
|_  start_date: N/A
```
<a href="/assets/images/htb-machine-writer/htb-machine-writer_nmap.png">
    <img src="/assets/images/htb-machine-writer/htb-machine-writer_nmap.png" alt="Hack The Box - Machine - Writer - Nmap">
</a>

## Check website
Open firefox to inspect website
```bash
firefox http://writer.htb &
```
<a href="/assets/images/htb-machine-writer/htb-machine-writer_website.png">
    <img src="/assets/images/htb-machine-writer/htb-machine-writer_website.png" alt="Hack The Box - Machine - Writer -Website">
</a>

## Fuzzing with gobuster
Find directories
```bash
gobuster dir -u "http://writer.htb/" -w "/usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt" -t 23 -o root_directories.fuzz
```
```txt
/about                (Status: 200) [Size: 3522]
/administrative       (Status: 200) [Size: 1443]
/contact              (Status: 200) [Size: 4905]
/dashboard            (Status: 302) [Size: 208] [--> http://writer.htb/]
/logout               (Status: 302) [Size: 208] [--> http://writer.htb/]
/server-status        (Status: 403) [Size: 275]
/static               (Status: 301) [Size: 309] [--> http://writer.htb/static/]
```

# Gain Access
Admin panel found in `/administrative`, you can login bypass use `admin' OR '1'='1` as username and `anypassword` as password.
<a href="/assets/images/htb-machine-writer/htb-machine-writer_adminpanel.png">
    <img src="/assets/images/htb-machine-writer/htb-machine-writer_adminpanel.png" alt="Hack The Box - Machine - Writer - Adminpanel">
</a>

# Privilege Escalation
