# Web Application Firewall (WAF) installation - ModSecurity


<img src="/static/mod/mod1.jpg" style="border-radius: 10px; width: 90%;"  />

---

## What is ModSecurity ?
In this post I will show you how to install and configure ModSecurity. So what is ModSecurity? ModSecurity is an open-source web application firewall (WAF) that provides protection for web applications by detecting and preventing various types of attacks. ModSecurity uses a rule set to detect and mitigate attacks. The OWASP Core Rule Set (CRS) is one of the most commonly used rule sets, offering protection against a wide range of threats, including SQL injection, cross-site scripting (XSS), and other common vulnerabilities.

---

## Installation and Configuration
For this post I have prepared a separate VM running Ubuntu and I have installed bWAPP on it. I have configured the server using Apache. As you can see I have tested a simple XSS attack:

<img src="/static/mod/mod2.png" style="border-radius: 10px; width: 70%;"  />
<br><br>

---

### Installing the ModSecurity module

<img src="/static/mod/mod3.png" style="border-radius: 10px; width: 70%;"  />

```bash
sudo apt install libapache2-mod-security2
```

Pretty straight forward, just use the command above, it will take only a few seconds.

---

### Configuring the ModSecurity module

Now it comes the configuration part. It is not that long as we will keep everything with a default configuration.

#### Copy and edit the config
We need to go to the ModSecurity directory first and then we must copy the **modsecurity.conf-recommended** to **modsecurity.conf**
<img src="/static/mod/mod4.png" style="border-radius: 10px; width: 70%;"  />

```bash
cd /etc/modsecurity/
```

```bash
sudo cp modsecurity.conf-recommended modsecurity.conf
```

Now we can use nano or any editor so we can edit **modsecurity.conf**.

<img src="/static/mod/mod5.png" style="border-radius: 10px; width: 70%;"  />

```bash
sudo nano modsecurity.conf
```

We can see that currently it is working in detection only mode : **SecRuleEngine DetectionOnly**

We should comment it out and add the following : **SecRuleEngine On** . Now we will be stopping requests containing SQL Injections or JavaScript.

<img src="/static/mod/mod6.png" style="border-radius: 10px; width: 70%;"  />

<br><br>
#### Backup the default CRS (Core Rule Set) and get the OWASP one

When we go to **/usr/share** we can see the directory named **modsecurity-crs**. We will back this up and download the OWASP CRS.
<img src="/static/mod/mod7.png" style="border-radius: 10px; width: 70%;"  />

```bash
cd /usr/share/
```

```bash
ls -la | grep modsec
```

The OWASP CRS is a set of generic attack detection rules for use with ModSecurity or compatible web application firewalls. The CRS aims to protect web applications from a wide range of attacks, including the OWASP Top Ten, with a minimum of false alerts.

<img src="/static/mod/mod8.png" style="border-radius: 10px; width: 70%;"  />
<br><br>
So let's backup the original folder before downloading the OWASP CRS:

```bash
sudo mv modsecurity-crs/ modsecurity-crs.backup
```

<img src="/static/mod/mod9.png" style="border-radius: 10px; width: 70%;"  />

<br><br>
#### Download the OWASP CRS and configure it

```bash
sudo git clone https://github.com/coreruleset/coreruleset.git modsecurity-crs
```

Now while we are in the **/usr/share** directory, we should download the CRS to a folder named **modsecurity-crs** (this is why we have backed up the original one)


<img src="/static/mod/mod10.png" style="border-radius: 10px; width: 70%;"  />

Once downloaded we should copy the config example which is located inside the folder:

```bash
sudo cp crs-setup.conf.example crs-setup.conf
```
For now we don't have to touch the file. We can use it with default configuration.

Now we should go to:
```bash 
cd /etc/apache2/mods-enabled/
```

The next step is to edit the **security2.conf** file.
<img src="/static/mod/mod11.png" style="border-radius: 10px; width: 70%;"  />

```bash 
sudo nano security2.conf
```

<img src="/static/mod/mod12.png" style="border-radius: 10px; width: 70%;"  />

Now once we open the config file, we should add 2 lines below 
**IncludeOptional /usr/share/modsecurity-crs/*.load**

Basically it should look like this:

```security2.conf 
<IfModule security2_module>
        # Default Debian dir for modsecurity's persistent data
        SecDataDir /var/cache/modsecurity

        # Include all the *.conf files in /etc/modsecurity.
        # Keeping your local configuration in that directory
        # will allow for an easy upgrade of THIS file and
        # make your life easier
        IncludeOptional /etc/modsecurity/*.conf

        # Include OWASP ModSecurity CRS rules if installed
        IncludeOptional /usr/share/modsecurity-crs/*.load
        IncludeOptional /usr/share/modsecurity-crs/*.conf
        IncludeOptional /usr/share/modsecurity-crs/rules/*.conf

</IfModule>

```
Once you have edited the file, you can save it. Sometimes there could be rules that are not working well, we can remove any of them that are not working correctly. They should be located inside of **/usr/share/modsecurity-crs/rules/NAMEOFRULE.conf**. You can remove it if not needed and it is causing errors.

Let's restart apache next once we have changed the config:
```bash
sudo systemctl restart apache2
```
---

## Let's test it out

First we will open one terminal and type

```bash
tail -f /var/log/apache2/error.log
```

Then let's go back to the XSS vulnerable page and test it out:

<img src="/static/mod/mod14.png" style="border-radius: 10px; width: 70%;"  />

If we type a normal text that should be entered there, we see it works without any issues. I have typed "test" in both fields and it seems that it was displayed back to me.


But what if we try attacking it?

```javascript
<script>alert('Pls install WAF');</script>
```

<img src="/static/mod/mod13.png" style="border-radius: 10px; width: 70%;"  />

<img src="/static/mod/mod15.png" style="border-radius: 10px; width: 70%;"  />

Well it seems that we have been blocked from doing that immediately 😎.

If we go back to the terminal where we have started a tail on the error.log, we can see the follwing :

<img src="/static/mod/mod16.png" style="border-radius: 10px; width: 90%;"  />

---

## Summary

After installing the WAF and testing the attack, ModSecurity effectively identified and blocked the XSS attempt on our bWAPP server by analyzing the input parameters and detecting the malicious activity. It applied its rules from the OWASP CRS, demonstrating the WAF’s capability to enhance the security of web applications by preventing common web-based attacks like XSS. In a real-life situation, there might be false positives, which could require additional tuning based on the specific case. We should keep our OWASP CRS updated, as well as our ModSecurity installation. Ensuring proper configuration and regular updates will help maintaining the effectiveness of the WAF.