# 1. Fawn

<img src="/static/htb/fawn/fawn.png" style="border-radius: 10px; width: 50%;"/>

In this post I will be attacking a machine in HTB which is named Fawn. It is considered to be a very easy machine so I plan on starting with the simple ones first and gradually increasing the difficulty with my other posts. I will go through each task related to the machine and I will document it here.

## Booting up and doing the tasks

First we will boot up the machine and get the IP address. Then after we are connected to the HTB VPN, we will check if we can ping it.
<img src="/static/htb/fawn/fawn2.png" style="border-radius: 10px; width: 50%;"/> <img src="/static/htb/fawn/fawn3.png" style="border-radius: 10px; width: 50%;"/>

Now time to get working on the tasks! The first few will be quite easy with no real practice so keep scrolling!

!!!success Task 1
What does the 3-letter acronym FTP stand for? 
> **File Transfer Protocol** (that's was quite the easy one 😎)
!!!

!!!success Task 2
Which port does the FTP service listen on usually? 
> **21** (very easy, if not sure you can always google that)
!!!

!!!success Task 3
What acronym is used for the secure version of FTP? 
> **SFTP** (still not feeling challenged so far 🥱)

Long story short SFTP provides encrypted file transfers over SSH.
!!!

!!!success Task 4
What is the command we can use to send an ICMP echo request to test our connection to the target? 
> **ping** (😴)
!!!

Now comes the more interesting part 🥳
!!!success Task 5
From your scans, what version is FTP running on the target? 
> Now the first thing that comes to my mind is to scan the target with nmap. Of course I will use nmap with **-sV** flag. What this flag does is that it makes nmap to perform service version detection.

<img src="/static/htb/fawn/fawn4.png" style="border-radius: 10px; width: 50%;"/>
!!!

!!!success Task 6
From your scans, what OS type is running on the target? 
> **Unix** (We can see that on the nmap scan on the previous step)
!!!

!!!success Task 7
What is the command we need to run in order to display the 'ftp' client help menu?  
> **ftp -h** (Well that's an easy one)
<br><br>
<img src="/static/htb/fawn/fawn5.png" style="border-radius: 10px; width: 50%;"/>
!!!

!!!success Task 8
What is username that is used over FTP when you want to log in without having an account? 
> **anonymous** (Well that makes sense. Let's try this on our target)
<br><br>
<img src="/static/htb/fawn/fawn6.png" style="border-radius: 10px; width: 50%;"/>
<br><br>
Seems to be working without a password.
!!!

!!!success Task 9
What is the response code we get for the FTP message 'Login successful'? 
> **230** (We can see that on the screenshot on task 8)
!!!

!!!success Task 10
There are a couple of commands we can use to list the files and directories available on the FTP server. One is dir. What is the other that is a common way to list files on a Linux system. 
> **ls** (That was easy! Let's try it out)
<br><br>
<img src="/static/htb/fawn/fawn7.png" style="border-radius: 10px; width: 50%;"/>
!!!

!!!success Task 11
What is the command used to download the file we found on the FTP server?
> **get** (That was very easy. Let's try it!)
<br><br>
<img src="/static/htb/fawn/fawn8.png" style="border-radius: 10px; width: 50%;"/>
!!!

!!!success SUBMIT FLAG
Submit root flag 
> **035db21c881520061c53e0536e44f815** (We can see the flag in the file that we have managed to download from the ftp.)
<img src="/static/htb/fawn/fawn9.png" style="border-radius: 10px; width: 50%;"/>
!!!

## Summary
After we have completed all tasks and we have gained the root flag, we have successfully pwned the machine. This seemed to be quite the easy one but as it was the first machine we have tested, it is kind of expected. I think this is a good exercise for beginners as some simple commands can be learned. Stay tuned for more!

<img src="/static/htb/fawn/fawn_pwned.png" style="border-radius: 10px; width: 50%;"/>