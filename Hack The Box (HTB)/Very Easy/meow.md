# 2. Meow (Tier 0)

<img src="/static/htb/meow/meow.png" style="border-radius: 10px; width: 40%;"/>

In this post I will be attacking Meow in HTB. It is considered to be a very easy machine so let's see how it goes!

---

## Booting up and doing the tasks

First we will boot up the machine and get the IP address and ping it as usual.
<img src="/static/htb/meow/meow2.png" style="border-radius: 10px; width: 50%;"/> <img src="/static/htb/meow/meow3.png" style="border-radius: 10px; width: 50%;"/>

Now let's start with the tasks. The first tasks could be quite boring as they are only simple questions, so if you are bored, you can just scroll down!

!!!success Task 1
What does the acronym VM stand for? 
> **Virtual Machine** (Obvious!)
!!!

!!!success Task 2
What tool do we use to interact with the operating system in order to issue commands via the command line, such as the one to start our VPN connection? It's also known as a console or shell. 
> **terminal** (Of course 🥱)
!!!

!!!success Task 3
What service do we use to form our VPN connection into HTB labs? 
> **openvpn** (You can use either openvpn on your machine to connect by downloading a config from the website or you can do it via a machine provided by HTB if you prefer - think it is for a limited time only)
!!!

!!!success Task 4
What tool do we use to test our connection to the target with an ICMP echo request? 
> **ping** (😴)
!!!

!!!success Task 5
What is the name of the most common tool for finding open ports on a target? 
> **nmap** (Of course!)
!!!

!!!success Task 6
What service do we identify on port 23/tcp during our scans? 
> **telnet** (We will do a nmap scan towards the host and see it)
<br><br>
<img src="/static/htb/meow/meow4.png" style="border-radius: 10px; width: 70%;"/>
!!!

!!!success Task 7
What username is able to log into the target over telnet with a blank password? 
> **root** (What I've learned so far from the courses I've been taking is that we should always try users such as root, admin, administrator, etc.)
<br><br>
<img src="/static/htb/meow/meow5.png" style="border-radius: 10px; width: 70%;"/> 
<br><br>
!!!


!!!success SUBMIT FLAG
Submit root flag 
> **b40abdfe23665f766f9c61ecba8a4c19** (We can see the flag when we ls and cat the file)
<img src="/static/htb/meow/meow6.png" style="border-radius: 10px; width: 50%;"/>
!!!

---

## Summary
Quite an easy machine, I think this is perfect for a beginner.

<img src="/static/htb/meow/meow_pwned.png" style="border-radius: 10px; width: 50%;"/>