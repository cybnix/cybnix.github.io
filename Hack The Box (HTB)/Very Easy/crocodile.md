# 3. Crocodile (Tier 1)

<img src="/static/htb/crocodile/croc.png" style="border-radius: 10px; width: 40%;"/>

In this post our target is Crocodile. Seems to be a very easy machine so let's get going!

---

## Booting up and doing the tasks

First we will boot up the machine, get the IP address and ping it.
<br></br>
<img src="/static/htb/crocodile/croc1.png" style="border-radius: 10px; width: 50%;"/> <img src="/static/htb/crocodile/croc2.png" style="border-radius: 10px; width: 50%;"/>

Now let's start with the tasks. The first tasks could be quite boring as they are only simple questions, so if you are bored, you can just scroll down!

!!!success Task 1
What Nmap scanning switch employs the use of default scripts during a scan? 
> **-sC** (This switch tells nmap to run some default scripts. As far as I am aware, these scripts are designed to perform various tasks like version detection, vulnerability detection, etc.)
!!!

!!!success Task 2
What service version is found to be running on port 21? 
> **vsftpd 3.0.3** (We can see that by running ***nmap -sC -sV 10.129.86.22***)
<img src="/static/htb/crocodile/croc3.png" style="border-radius: 10px; width: 50%;"/>
!!!

!!!success Task 3
What FTP code is returned to us for the "Anonymous FTP login allowed" message?  
> **230** (We can see that when logged in as anonymous)
<br><br>
<img src="/static/htb/crocodile/croc4.png" style="border-radius: 10px; width: 50%;"/>
!!!

!!!success Task 4
After connecting to the FTP server using the ftp client, what username do we provide when prompted to log in anonymously?
> **anonymous** (Already done in the previous step!)
!!!

!!!success Task 5
After connecting to the FTP server anonymously, what command can we use to download the files we find on the FTP server? 
> **get** (We did that during our previous machines.)
<br><br>
<img src="/static/htb/crocodile/croc5.png" style="border-radius: 10px; width: 50%;"/>
!!!

!!!success Task 6
What is one of the higher-privilege sounding usernames in 'allowed.userlist' that we download from the FTP server? 
> **admin** (Shocking, right? 😋)
<br><br>
<img src="/static/htb/crocodile/croc6.png" style="border-radius: 10px; width: 40%;"/>
!!!

!!!success Task 7
What version of Apache HTTP Server is running on the target host? 
> **Apache httpd 2.4.41** (We can see that on the nmap scan we did previously on Task2.)
!!!

!!!success Task 8
What switch can we use with Gobuster to specify we are looking for specific filetypes? 
> **-x** (Had to look that up as it is not presented in the -h option of gobuster. Seems that we can learn something new even from the easiest machines 🎉)
!!!

!!!success Task 9
Which PHP file can we identify with directory brute force that will provide the opportunity to authenticate to the web service? 
> **login.php** (We found it by using gobuster with one of the wordlists that come with Kali + the -x flag from the previous step)
<img src="/static/htb/crocodile/croc7.png" style="border-radius: 10px; width: 70%;"/>
<img src="/static/htb/crocodile/croc8.png" style="border-radius: 10px; width: 70%;"/>
!!!

!!!success SUBMIT FLAG
Submit root flag 
> **c7110277ac44d78b6a9fff2232434d16** (We have managed to get that by going to http://10.129.86.22/login.php and logging in with admin username and one of the passwords that we got from the files in the FTP earlier)
<img src="/static/htb/crocodile/croc9.png" style="border-radius: 10px; width: 50%;"/>
<img src="/static/htb/crocodile/croc10.png" style="border-radius: 10px; width: 70%;"/>
!!!

---

## Summary
Although this machine was easy, I've managed to learn about the -x flag used in gobuster. The machine was pwned and we have obtained the root flag.

<img src="/static/htb/crocodile/croc_pwn.png" style="border-radius: 10px; width: 50%;"/>