---
title: Firewall Bypass
description: How to bypass IDS and Deep Packet Filters on almost any firewall.
slug: firewall-bypass
date: 2025-01-22 00:00:00+0000
---


***This is for education purposes only, use at your own risk******This is for education purposes only, use at your own risk***

## This guide will speak about how to bypass almost all firewalls with a stable ssh tunnel (with optional domain name and pub-key authentication)

## This guide will speak about how to bypass almost all firewalls with a stable ssh tunnel (with optional domain name and pub-key authentication)

For this to work you will need:

1. A remote ssh server (can be linux, windows, android, whatever, as long as you have a ssh server that is connected to another network than the target) **I STRONGLY RECOMMEND USING A VM**
2. Port forwarding / access to the router's configuration page. **I RECOMMEND A DMZ**
3. A firewalled internet connection.
4. A ssh client installed on your device capable of doing ssh tunneling.
5. Some minimal linux / networking knowledge
6. Common sense
7. Optionally: a domain name

#### First, you need to know the outgoing port and protocol allowed by your target network (in my case it was *22* with the *ssh* protocol allowed). This will most likely be the hardest and most complicated part to do beforehand. I will not explain how to do this as it is out of scope and not part of this guide.

Then, you can do the following on your remote (personal computer / vm / phone) ssh server. I will be using linux as the OS of the server and Debian as the distro.

First, edit the ssh server config permit password authentication (should be activated by default)

```sudo nano /etc/ssh/sshd_config```

And then change from

```conf
#PasswordAuthentication yes

to

PasswordAuthentication yes
```

`ip a`

```
1: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether [redacted] brd ff:ff:ff:ff:ff:ff permaddr [redacted]
    inet 192.168.2.63/24 brd 192.168.2.255 scope global dynamic noprefixroute wlan0
       valid_lft 1410sec preferred_lft 1410sec
```
In my case, my internal (private) ip is 192.168.2.63

Next, it to setup a port forward on your external ip to your ssh server using the outgoing port as your external internet-facing port and 22 as your internal port pointing to your server's internal ip.

On opnsense, it should look like this:
![Image 1](image.png)

```sudo systemctl enable --now sshd.service```

And curl your external ip, if ever you forgot it.

```curl ifconfig.me```

`[example_ip]`

And, you're done! Just for the server's side however.

Install "sshuttle" on your client.

```sudo apt install sshuttle```

On the client, now simply connect in this fashion:

(Replace example_ip by your external ip)

```sudo sshuttle --dns -vr [your_user_name]@[example_ip]:22 0/0 -x [example_ip]```

Login using your username and password as you normally would on your server.

And there you have it, a ssh tunnel from your target's network to your remote server, essentially bypassing every restriction/firewall they would have in place.

# (OPTIONAL) More advanced configuration with a domain name and pub-key authentication.

First, setup a "A" dns record using your provider of choice pointing to your public ip.

I will be using ssh.example.com as the domain name

```sudo sshuttle --dns -vr [your_user_name]@ssh.example.com:22 0/0 -x ssh.example.com```

Quite simple but effective, in case you cant remember your ip.

But in this case you would be exposing a ssh server to the internet using a domain name, making it even simpler for attackers to use a zero-day exploit on your vm/machine. This is why i recommend setting up a **DMZ** and a vm/docker container to make it contained and seperate from your home network in case of infiltration by an adversary.

Next, we will be using pub-key authentication to login into our ssh server from now on. 

From your client, do 

```ssh-copy-id [your_user_name]@[example_ip] or (ssh.example.com)```

And from now on, you will not need to use a ssh password. Just dont loose your private key, or misplace it as anyone with this private key can now access your server.

To conterviene this, use a password protected ssh private key.

```
ssh-keygen                                       
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/[your_user_name]/.ssh/id_ed25519): 
Enter passphrase for "/home/[your_user_name]/.ssh/id_ed25519" (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/[your_user_name]/.ssh/id_ed25519
Your public key has been saved in /home/[your_user_name]/.ssh/id_ed25519.pub
```

Now use the following command to connect:

```bash
sudo sshuttle --dns -vr [your_user_name]@ssh.example.com:22 0/0 -x ssh.example.com --ssh-cmd 'ssh -i /home/[your_user_name]/.ssh/id_ed25519'
```