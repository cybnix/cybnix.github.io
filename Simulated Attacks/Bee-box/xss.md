# Cross-Site Scripting (XSS)

[How to configure bWAPP with Bee box for your own use](/Guides/EH-Tools-and-Utilities/bwapp)

In this post, I will present some examples of Cross-Site Scripting (XSS) vulnerabilities in bWAPP. XSS is a type of vulnerability that occurs when attackers inject malicious scripts into web pages viewed by other users. These scripts can execute arbitrary code in the context of a user's browser, leading to various malicious activities such as stealing sensitive information or hijacking user sessions.

## Cross-Site Scripting - Reflected (GET)

This vulnerability is "reflected" because the injected script is reflected back to the user in the server's response. Basically, an attacker crafts a malicious URL containing a script as a parameter value, and when the user clicks on the link, the script will get executed in the browser of the user.


<img src="/static/xss/xss1.png" style="border-radius: 10px; width: 80%;"  />
<br><br>

We have 2 fields here, once we type stuff in them, we get a message below saying "welcome" + the information that we have typed. So what now? Let's try a simple javacscript that will show a popup:

```javascript 
<script>alert('TEST');</script>
``` 
<img src="/static/xss/xss2.png" style="border-radius: 10px; width: 80%;"  />

Seems to be working and since this is using a GET method, we can send this URL to anyone and if he opens that, he will get the same popup. The link will look like that :

```
http://192.168.160.131/bWAPP/xss_get.php?firstname=%3Cscript%3Ealert%28%27TEST%27%29%3B%3C%2Fscript%3E&lastname=test&form=submit
```

And why is that dangerous you may ask? Well an attacker can use a bit more complicated attack, where he can steal the cookies of the user for example and he can do a Session Hijacking and this could lead to unauthorized actions like changing passwords, seeing private information and basically stealing the account.

For example if we execute :

```javascript
<script>alert(document.cookie);</script>
```
A popup with the session cookies appears:
<img src="/static/xss/xss3.png" style="border-radius: 10px; width: 80%;"  />

So basically a hacker can redirect those cookies to his own server and steal them.

---

## Cross-Site Scripting - Reflected (POST)

The POST one is a bit different because we cannot see it in the url:
<img src="/static/xss/xss4.png" style="border-radius: 10px; width: 80%;"  />
```javascript
<script>alert(document.cookie);</script>
```
It is still very dangerous, as in the case of a POST-based XSS vulnerability, the attacker can exploit it by creating a malicious form (on their website, for example) that submits data to the vulnerable site. If the attacker baits the victim into using their own malicious website, they can still achieve their goal, such as stealing cookies or other sensitive data.

---

## Cross-Site Scripting - Stored (Blog)

WIP