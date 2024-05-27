# Cracking WPA/WPA2
---

## Introduction

<img src="/static/bird1.jpg" style="border-radius: 10px;"/>

In this post, I will explore how to conduct a simulated WiFi attack on a test network to understand how credentials can be stolen and used to gain unauthorized access. The goal is to learn about the dangers of such attacks and how they can affect us. I will be using my Kali Linux laptop which I have for testing purposes, however the tools I am using are available for other operating systems as well. They come preinstalled in Kali which is why I prefer using it.

While there are many resources available on this topic, I wanted to present it in my own way for better clarity and understanding, and to have it as a future reference for myself as well 😇.

!!!warning 
Please remember, never perform these actions on networks you do not own or have explicit permission to test.
!!!

---

## We need to put our wireless interface in monitor mode first

#### What's monitor mode? Why do we need that?

Monitor mode would allow our WiFi card to listen to all wireless trafic and not just the data addressed to us. This is essential for capturing important information like the WPA/WPA2 handshake for example, which we will show later. 

#### Do all adapters support monitor mode?

Nope! It really depends on the hardware and the driver of the adapter. Before proceeding, it's good to check if your WiFi adapter is compatible. Many people use external USB adapters that support monitor mode, and there are some really cheap ones available online. You can find a lot of information about compatible adapters, but I suggest looking into the following resources : [!button size="m" corners="pill" variant="success" text="Github"](https://github.com/morrownr/USB-WiFi/blob/main/home/USB_WiFi_Chipsets.md) [!button size="m" corners="pill" variant="success" text="Wirelesshack"](https://www.wirelesshack.org/best-kali-linux-compatible-usb-adapter-dongles.html) 

I have one really old laptop that luckily has an adapter (Atheros AR9485) that supports it and I use it for testing. I also have one external adapter which I got for around $10 some time ago but I won't be using it for the current post, maybe at a later stage 😎. 

#### Putting my adapter into monitor mode

<img src="/static/airmon1.png" style="border-radius: 10px;"/>

First we can see that our adapter is "wlan0" after using "iwconfig" and it is in Mode: Managed.

<img src="/static/airmon2.png" style="border-radius: 10px;"/>

Now after using **sudo airmon-ng start wlan0** we can see how the adapter changed its name to "wlan0mon" and its mode to "Montior".

That's it. Quite simple isn't it? Now our adapter can capture the needed information.

---

## Capturing the 4-way authentication handshake

> If  you don't know what 4-way handshake is, this is simply the series of messages that are being exchanged between your device and the network so a secure connection can be established. While it is designed for a secure authentication, we can still capture that and we can attempt to crack it which can lead to us gaining unauthorized access to the network. 🤖

### Scan for the target

<img src="/static/airodump-ng.png" style="border-radius: 10px;"/>

We should now use **sudo airodump-ng wlan0mon**. This would show us the nearby networks and some details related to them. As you can see on the above screenshot, we have identified our target and we know which channel it uses + its MAC address as well.

### Select the target and capture the handshake

Next, we need to capture the handshake from our target WiFi network. To do this, we'll use the airodump-ng command. In this example, we're targeting a network on channel 7 with a specific BSSID (32:DE:4B:47:85:8F). The captured data will be saved to the desktop and then we can continue further.

>**sudo airodump-ng -c 7 -w /home/user/Desktop/capture -d 32:DE:4B:47:85:8F wlan0mon**

> - ***-c*** we select the channel of the network we would like to attack
> - ***-w*** the path of our capture file
> - ***-d*** MAC address of the access point
> - ***wlan0mon*** our adapter which is now in monitor mode
> - ***We got all the above information from our first step which was to scan the nearby networks.***

<img src="/static/airodump-ng-listen.png" style="border-radius: 10px;"/>

Now we will see the above window after executing the command. Once a device tries to connect to the AP, we will capture the handshake. 

### But what if there are no devices that want to connect to the network at the current moment?

We can easily force the ones that are already connected to try to reconnnect to the access point and then we can capture the handshake.😉

We will send some messages to the clients, which are basically going to tell them that they are no longer associated with this access point. This will make them try to reconnect and it would let us capture the handshake.

### Executing a deauthentitcation attack

First we will connect the "victim" to the test network which we are using for the attack.

<img src="/static/client.jpg" style="border-radius: 10px;"/>

Now that we have the needed details from the first scan we can proceed further with the following command :

> **sudo aireplay-ng --deauth 0 -a 32:DE:4B:47:85:8F wlan0mon**

> - ***aireplay-ng*** this tool is a part of the aircrack-ng suite as well as the other which we have used so far
> - ***--deauth 0*** this specifies that we want to do a deauthentication attack, 0 simply means that packets will be sent until manually stopped
> - ***-a*** MAC address of the access point
> - ***wlan0mon*** our adapter which is now in monitor mode
> - ***With an additional flag "-c" we can specify a client that we want to deauth from the network. You will usually get a list of the clients on the previous steps but in our case we will just do it without it as our test network has only 1 device connected to it. As you can see in the disclaimer on the below screenshot, it is said that this attack is more effective if we specifically target a client that is connected to the network.***

<img src="/static/deauth1.png" style="border-radius: 10px;"/>

Now what happens after executing this attack is that our phone got disconnected from the network. When we stop the deauth attack, the phone will automatically connect back and in the window where we are waiting for the handshake, we will see a sign that it is collected.

<img src="/static/handshake.png" style="border-radius: 10px;"/>
<br/><br/>
Now we don't even need to be close to the network as the cracking process can be done offline as we got the capture file on our desktop.

<img src="/static/capture.png" style="border-radius: 10px;"/>

---
## Cracking the handshake

Now that we got the .cap file that contains the handshake, we can try to crack it.

<img src="/static/rockyou.png" style="border-radius: 10px;"/>
<br/><br/>

> **sudo aircrack-ng capture-01.cap -w /usr/share/wordlists/rockyou.txt**
> - We just have to use aircrack-ng, the .cap file and a wordlist. In this case you can see that I am using "rockyou.txt" which is a wordlist included in Kali Linux. You can use whatever you like as a lot of wordlists are widely available on the internet. Or you can maybe even generate one yourself. Thinking about it, I might create a simple guide for that in the future 😎.

<img src="/static/cracked.png" style="border-radius: 10px;"/>

As you can see, after we have executed the command it took only 6 seconds to crack this password. Aircrack uses each password in our wordlist as a key to attempt to decrypt the encrypted handshake information.

<img src="/static/pass.png" style="border-radius: 10px;"/>
<br/><br/> 
<img src="/static/wifi.png" style="border-radius: 10px;"/>

And as you can observe above, we were able to connect to this network with the provided password.

---
## Opening the .cap file in Wireshark to see each step of the handshake

If you find it interesting, you can open this .cap file with Wireshark. That way you can observe the authentication process between the client and the AP. You can see the individual hanshake messages which have been exchanged during the authentication process.

It's very straightforward, all you have to do is open Wireshark, open the file :
<img src="/static/wireshark1.png" style="border-radius: 10px;"/>

Next filter for "eapol" (Extensible Authentication Protocol over LAN) packets. When a device tries to connect to a WiFi network, it needs to prove its identity to the access point (the router). EAPOL packets are like the 'handshake' between the device and the access point during this authentication process.

<img src="/static/wireshark2.png" style="border-radius: 10px;"/>

---

## Protecting yourself

A few tips that I follow to protect myself :

**Strong password**
:   I would say that this is a must. Make sure the password combines uppercase with lowercase letters, numbers and even special characters. It would be significantly harder to crack one as it is going to be more resistant to dictionary-based attacks. Plus I wouldn't use any common words 😁

**Enable WPA3**
:   WPA3 provides stronger encryption and authentication protocols compared to previous standards like WPA and WPA2 (had to downgrade mine to WPA2 for the simulation 😄). It introduces the Simultaneous Authentication of Equals (SAE) protocol, which protects against various types of attacks, including brute-force and dictionary attacks.

**MAC Address Filtering**
:   Restrict the access to your WiFi network by only allowing devices with specific MAC addresses to connect to it. Yes a MAC address can definitely be spoofed but in my opinion it would add an additional barrier. I would like to make it as annoying as possilble if anyone is attempting to breach into my home network 😉.