# Cracking WPA/WPA2

## Introduction

<img src="/static/bird1.jpg" style="border-radius: 10px;"/>

In this post, I will explore how to conduct a simulated WiFi attack on a test network to understand how credentials can be stolen and used to gain unauthorized access. The goal is to learn about the dangers of such attacks and how they can affect us. I will be using my Kali Linux laptop which I have for testing purposes, however the tools I am using are available for other operating systems as well. They come preinstalled in Kali which is why I prefer using it.

While there are many resources available on this topic, I wanted to present it in my own way for better clarity and understanding, and to have it as a future reference for myself as well 😇.

!!!warning 
Please remember, never perform these actions on networks you do not own or have explicit permission to test.
!!!

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

Now after using "sudo airmon-ng start wlan0" we can see how the adapter changed its name to "wlan0mon" and its mode to "Montior".

That's it. Quite simple isn't it? Now our adapter can capture the needed information.

## Capturing the 4-way authentication handshake

#### Scan for the target

<img src="/static/airodump-ng.png" style="border-radius: 10px;"/>

We should now use "sudo airodump-ng wlan0mon". This would show us the nearby networks and some details related to them. As you can see on the above screenshot, we have identified our target and we know which channel it uses and we know the MAC address as well.