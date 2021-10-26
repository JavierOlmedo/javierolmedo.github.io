---
layout: single
title: '<span class="post">Create your own log-free VPN</span>'
excerpt: "Having a log-free personal VPN allows you to maintain an acceptable level of anonymity and privacy on the network, this post will show you how to configure one with DigitalOcean."
date: 2020-11-24
header:
  teaser: /assets/images/create-your-own-log-free-vpn/create-your-own-log-free-vpn_banner.png
  teaser_home_page: true
  icon: /assets/images/post.svg
categories:
  - handbooks
tags:
  - digitalocean
  - openvpn  
  - vpn
toc: true
toc_label: "Content"
toc_sticky: true
show_time: true
---

<!-- BANNER -->
<a href="/assets/images/create-your-own-log-free-vpn/create-your-own-log-free-vpn_banner.png">
    <img src="/assets/images/create-your-own-log-free-vpn/create-your-own-log-free-vpn_banner.png" alt="Create your own log-free VPN">
</a>

<!-- PREFACE -->
# Preface
Welcome to the tutorial on **how to create our own log-free VPN** by setting up an OpenVPN server on a cloud machine.

Before starting with the tutorial, you must create a machine in the cloud, for this tutorial, we have chosen to use a **DigitalOcean VPS** with an Ubuntu 20.04 machine, which has a cost of \$5 per month, through the following [link](https://m.do.co/c/67dd38080d62) you can get **$100 for free**.


<!-- CONTENT -->
# Creating a cloud machine

As I mentioned at the beginning, I have chosen DigitalOcean as VPS, but there are others like Linode, Azure or AWS. To create an Ubuntu 20.04 machine we must go to the top right of our panel and select `Create -> Droplets`.

<a href="/assets/images/create-your-own-log-free-vpn/create-your-own-log-free-vpn_001.png"><img src="/assets/images/create-your-own-log-free-vpn/create-your-own-log-free-vpn_001.png"></a>

We will choose the following image with the cheapest plan (**Ubuntu 20.04 for 5$ per month**, remember you get 100$ free if you use my link). We can also choose the location of our server, in my case I will use Frankfurt (Germany) and create credentials for the root user.

<a href="/assets/images/create-your-own-log-free-vpn/create-your-own-log-free-vpn_002.png"><img src="/assets/images/create-your-own-log-free-vpn/create-your-own-log-free-vpn_002.png"></a>

# Generate SSH keys

In the previous step, we created a password for the root user, but using unencrypted passwords to log in to our machine is not a good idea since they can be exposed on the network because they are not encrypted. Therefore, we will generate keys so that only machines that have them (and the password) can log in.

We can generate the keys in the following way:

- Windows (with Powershell):

```ps1
PS C:\> Add-WindowsCapability -Online -Name OpenSSH.Client*
```

- Linux and Mac:

```bash
ssh-keygen -t rsa -b 4096
```

<a href="/assets/images/create-your-own-log-free-vpn/create-your-own-log-free-vpn_003.png"><img src="/assets/images/create-your-own-log-free-vpn/create-your-own-log-free-vpn_003.png"></a>
