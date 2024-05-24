# 4. Appointment

<img src="/static/htb/appointment/appointment.png" style="border-radius: 10px; width: 40%;"/>

In this post our target is Appointment. From the description it seems that this is a DB related one.

## Booting up and doing the tasks

Booting up the machine and pinging it. I will be using ParrotOS for this one instead of Kali (just testing).
<br></br>
<img src="/static/htb/appointment/appointment2.png" style="border-radius: 10px; width: 40%;"/> <img src="/static/htb/appointment/appointment3.png" style="border-radius: 10px; width: 70%;"/>

Time for the tasks related to this machine.

!!!success Task 1
What does the acronym SQL stand for? 
> **Structured Query Language** (easy!)
!!!

!!!success Task 2
What is one of the most common type of SQL vulnerabilities?  
> **sql injection** (we all know that, don't we 😏)
!!!

!!!success Task 3
What is the 2021 OWASP Top 10 classification for this vulnerability?  
> **A03:2021-Injection** ([OWASP top 10](https://owasp.org/Top10/))
!!!

!!!success Task 4
What does Nmap report as the service and version that are running on port 80 of the target? 
> **Apache httpd 2.4.38 ((Debian))** (We ran a Nmap scan to verify that)
<br><br>
<img src="/static/htb/appointment/appointment4.png" style="border-radius: 10px; width: 60%;"/>
!!!

!!!success Task 5
What is the standard port used for the HTTPS protocol?  
> **443** (easy!)
!!!

!!!success Task 6
What is a folder called in web-application terminology?  
> **directory** (another easy one!)
!!!

!!!success Task 7
What is the HTTP response code is given for 'Not Found' errors? 
> **404** (We have all seen that 😖 - https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)
!!!

!!!success Task 8
Gobuster is one tool used to brute force directories on a webserver. What switch do we use with Gobuster to specify we're looking to discover directories, and not subdomains?  
> **dir** (we can check that with **gobuster -h**)
<br><br>
<img src="/static/htb/appointment/appointment5.png" style="border-radius: 10px; width: 60%;"/>
!!!

!!!success Task 9
What single character can be used to comment out the rest of a line in MySQL? 
> **#** (easy!)
!!!

!!!success Task 10
If user input is not handled carefully, it could be interpreted as a comment. Use a comment to login as admin without knowing the password. What is the first word on the webpage returned?  
> **Congratulations** (check the description below)
<br><br>
Now it is very important that we use **admin'#** instead of **admin#** as user for this to work. It took me some time to realise that. The single quote will let the SQL server know where the username ends, it's like we use a period in a sentence. If we don't use the quote, SQL will think that the user is "**admin#**". This was a bit tricky!

<img src="/static/htb/appointment/appointment6.png" style="border-radius: 10px; width: 60%;"/>
!!!

!!!success SUBMIT FLAG
Submit root flag 
> **e3d0796d002a446c0e622226f42e9672** (we got it after logging in)
<img src="/static/htb/appointment/appointment7.png" style="border-radius: 10px; width: 60%;"/>
!!!

## Summary
All tasks were pretty easy. However it took me some time to realise what is happening on step 10. Overall I think it is a nice machine for a beginner.

<img src="/static/htb/appointment/appointment8.png" style="border-radius: 10px; width: 60%;"/>