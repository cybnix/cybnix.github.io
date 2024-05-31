# MITM WPA/WPA2 attack - Fluxion
---

## Introduction 

<img src="/static/parrot.jpg" style="border-radius: 30px; width: 100%;"/>
<br><br>

In this post I will be simulating an attack with Fluxion on my own test network at home. The goal is to present how we can simulate a rogue access point, tricking unsuspecting users into revelaling their WiFi credentials to us. If a hacker cannot crack your handshake, he can use tools like fluxion that are using social engineering to steal a password. I will be using my laptop which I have used in [Cracking WPA/WPA2](/Simulated-Attacks/Network-and-Wireless-Attacks/crackingwpa). If you want more information on adapters you can check that post as I have shared some details there.

!!!warning 
Please remember, never perform these actions on networks you do not own or have explicit permission to test.
!!!
---

## We need to start our fluxion first

If you want to find out how to configure your fluxion I recommend checking my guide for that - [Fluxion Setup](/Guides/EH-Tools-and-Utilities/fluxion_install)

When we first start our fluxion we are first presented with a language option to select :
<img src="/static/fluxion/fluxion4.png" style="border-radius: 10px; width: 80%;"/>

After we select the language, we need to select another option. We have 2 of them, one is to create a Captive Portal or an "evil twin" where the victim will connect and the other one is a Handshake Snooper. So in order to create an evil twin, we need to first use the Handshake Snooper as we need the handshake because it will be used later for password verification (don't worry everything is semi-automated here so it's quite easy to do).

<img src="/static/fluxion/fluxion5.png" style="border-radius: 10px; width: 80%;"/>
<br><br>

---

## Capturing the handshake

We need to select a channel now, I will go with All channels (2.4GHz). In our case I know that my TestNetwork is 2.4GHz (7 channel) as I have configured it that way. There is a device already connected to it so it should be quite easy to get the handshake.

<img src="/static/fluxion/fluxion6.png" style="border-radius: 10px; width: 80%;"/>
<br><br>

A scan will pop up so after we see our targeted network populated, we will stop the scan and select the network - in our case **TestNetwork**.

<img src="/static/fluxion/fluxion7.png" style="border-radius: 10px; width: 80%;"/>
<br><br>

Now we need to select the interface which will be using. In my case I have only 1 which is my internal adapter.

<img src="/static/fluxion/fluxion8.png" style="border-radius: 10px; width: 80%;"/>
<br><br>

Now as I did in [Cracking WPA/WPA2](/Simulated-Attacks/Network-and-Wireless-Attacks/crackingwpa), I will deauthenticate the devices on this network to speed up the process so I will go with the third option. I am going with it as I have had more success with the mdk4 method of deauthentication.

<img src="/static/fluxion/fluxion9.png" style="border-radius: 10px; width: 80%;"/>
<br><br>

Now we need to select a method of verification for the hash. Fluxion seems to be recommending cowpatty so let's try that.
<img src="/static/fluxion/fluxion10.png" style="border-radius: 10px; width: 80%;"/>

Will go with every 30 seconds, want to speed it up a bit.
<img src="/static/fluxion/fluxion11.png" style="border-radius: 10px; width: 80%;"/>

Now we will go with Asynchronous verification. What does that mean? The difference between the two is that during the Asynchronous verification, multiple verifications processes run in parallel, which can make the attack faster. In synchronous however, processes run one after the other in a sequence, which would be slower. 
<img src="/static/fluxion/fluxion12.png" style="border-radius: 10px; width: 80%;"/>

A few windows will pop up showing you the processess that are happening and one of them being the deauth packets being sent. So what happened is that my phone which was connected to the network got disconnected and upon trying to connect back again automatically, the handshake was captured.

<img src="/static/fluxion/fluxion13.png" style="border-radius: 10px; width: 80%;"/>
<img src="/static/fluxion/fluxion13-1.png" style="border-radius: 10px; width: 80%;"/>
<br><br>

---

## Creating an evil twin with the captured handshake
Now as we have it saved to the fluxion's database, we can go back and do another attack, which is the Captive Portal.
<img src="/static/fluxion/fluxion14.png" style="border-radius: 10px; width: 80%;"/>

Again, we have to do the scan and select our test network.
<img src="/static/fluxion/fluxion15.png" style="border-radius: 10px; width: 80%;"/>

Once again, we will select our adapter as it will be needed to create that "evil" access point.
<img src="/static/fluxion/fluxion16.png" style="border-radius: 10px; width: 80%;"/>
<img src="/static/fluxion/fluxion17.png" style="border-radius: 10px; width: 80%;"/>

Then we have to select our method of authentication, which will be used to deauthenticate the devices on the network while spawning the evil access point. We will go with mdk4 again.

<img src="/static/fluxion/fluxion18.png" style="border-radius: 10px; width: 80%;"/>
<br><br>

Next step is to pick a tool for the AP. We will go with hostapd as I've read it is more stable and can handle multiple connections. (I've tested it with 2 phones and it really did handle them)

<img src="/static/fluxion/fluxion19.png" style="border-radius: 10px; width: 80%;"/>

As in one of the previous steps, we will go again with cowpatty that will be our password verification method, which will basically compare the user key to the handshake. So here comes the question what happens if the user types a wrong password on the fake AP? Well the tool will compare the password to the handshake which we have captured earlier so if the password is not correct, the user will continue to be prompted. In case the password is correct, the AP will shutdown and everything will go back to normal so the user won't find out that he was basically tricked.

<img src="/static/fluxion/fluxion20.png" style="border-radius: 10px; width: 80%;"/>
<br><br>

Now we will use the hash that we have captured earlier for the evil twin AP. [1]

<img src="/static/fluxion/fluxion21.png" style="border-radius: 10px; width: 80%;"/>
<br><br>

As in the previous step we will use the hash verification to be cowpatty (will stick to it as it is recommended). 

<img src="/static/fluxion/fluxion22.png" style="border-radius: 10px; width: 80%;"/>
<br><br>

Next step is to generate a captive portal. Basically this portal will look very real and once the user connects to the fake AP, he will be presented a portal which will bait him to type in the network password. We will create an SSL certificate so it looks more legit.

<img src="/static/fluxion/fluxion23.png" style="border-radius: 10px; width: 80%;"/>
<br><br>

At this step we will use the "disconnected" which won't provide access to the internet to our victim once he connects to our fake AP.
<img src="/static/fluxion/fluxion24.png" style="border-radius: 10px; width: 80%;"/>

This part is really, really cool. We can select from a ton of interfaces for a bait. I will go with a Generic one in Bulgarian language for this test. There are 79 different portal versions currently.

<img src="/static/fluxion/fluxion25.png" style="border-radius: 10px; width: 80%;"/>
<br><br>

Now what we see are a bunch of windows doing different things. There is a DNS server, redirecting all request to the attacker's host portal. A web server that is serving the portal to the victim. Also we got a jammer (the red one) that will continue sending deauth requests to drop all clients from the original AP. So what will happen now is tht all authentication attempts at the fake portal will be checked against the handshake we got earlier. Once everything is ready, all services will be terminated nd we will get the key.

<img src="/static/fluxion/fluxion26.png" style="border-radius: 10px; width: 100%;"/>
<br><br>

Now we wait.

---

## What happens next when the victim connects?

From the perspective of our victim, we can see the following - 2 networks with the same name, the first one is unlocked (evil twin one) and the victim is not able to connect to the second one (the real network) as it is being deauthenticated constantly:

<img src="/static/fluxion/phone1.png" style="border-radius: 10px; width: 30%;"/>
<br><br>

The victim will be redirected into the browser to our fake portal page. So basically this message tells the victim that for security reasons, it is needed to enter the password to gain access to the internet.

<img src="/static/fluxion/phone2.png" style="border-radius: 10px; width: 30%;"/>
<br><br>

Now as the victim enters the password a message appears that the connection will be recovered in a few seconds.

<img src="/static/fluxion/phone3.png" style="border-radius: 10px; width: 30%;"/>

---

## Getting the password on our end

So when the victim connected to our rogue AP first, we have noticed that in some of the terminal windows that have appeared. It looks like that the victim is being redirected to the portal and we can see it on the DNS Service and Web Service windows.
<img src="/static/fluxion/fluxion29.png" style="border-radius: 10px; width: 60%;"/>

Once the victim entered the password in the fake portal, a window appeared that told us where the password got saved.
<img src="/static/fluxion/fluxion30.png" style="border-radius: 10px; width: 70%;"/>

And there we have it.

<img src="/static/fluxion/fluxion31.png" style="border-radius: 10px; width: 70%;"/>

---

## Conclusion

The goal of this simulation was to present how vulnerable we can be to targeted attacks. By creating a rogue access point, attackers can trick people into revealing their WiFi passwords, which will grant them unauthorized access to the network. It's very important for us to understand how those methods work so we can recognize such an attack.

Always enable WP3 encryption if supported. It offers stronger security features that can protect against many common attacks.

Regularly update all of your devices including the firmware of your router. For example I have tried to connect to this network from one phone which has the latest updates and it didn't work. However I have tested 2 other phones which have not been updated in a while and guess what? Both of them were able to connect to the evil twin network.

Believe me, such an attack can easily work in real life, especially if the victims are people who are not aware of these types of security threats.

Stay informed, stay secure and be sure to inform the people around you. 😎