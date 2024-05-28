# Fluxion Setup

<img src="/static/fluxion.jpg" style="border-radius: 10px; width: 60%;"/>

---

## So what is Fluxion?

It's a tool focused on obtaining a WiFi password via a phishing attack. If you've never seen it, you'll be surprised how easy it is to use. I will present this in another post but I will show you how to configure it in the current one.

!!!info 
It's very important that your adapter supports monitoring mode. For more information on that you can check my other post [Cracking WPA/WPA2](/Simulated-Attacks/Network-and-Wireless-Attacks/crackingwpa)
!!!

---

## How to get it?

First you should go to your terminal and type :
>- sudo git clone https://www.github.com/FluxionNetwork/fluxion.git

<img src="/static/fluxion/fluxion1.png" style="border-radius: 10px; width: 80%;"/>

Next step is to be in the directory where you have downloaded the tool.
>- Try to run it with **sudo ./fluxion.sh**

<img src="/static/fluxion/fluxion2.png" style="border-radius: 10px; width: 80%;"/>

You will see the above window. It is quite possible that there are missing dependencies. To fix that you should run :

>- **sudo ./fluxion -i**

After you fix the dependencies everything should be working fine.

<img src="/static/fluxion/fluxion3.png" style="border-radius: 10px; width: 80%;"/>

<br><br>
!!!info Tip
You don't need to navigate to the folder to start it every time. You can edit your shell configuration and make an alias for that so you can type "fluxion" and run it from everywhere. I really like to create aliases because it simplifies my work. 😎
!!!

---

## How to add an alias?

1. First you need to edit your shell config file. In my case I am using zsh so I need to edit it the following way:

>- nano ~/.zshrc

2. At the bottom of the file I will add the following :

>- '# fluxion alias
<br><br>
alias fluxion='cd /opt/fluxion/ && sudo ./fluxion.sh' '
<br><br>
You can adjust the path based on the location of fluxion in your case.

<img src="/static/fluxion/alias.png" style="border-radius: 10px; width: 80%;"/>

<br><br>
3. Save it. Now you can just type "fluxion" from anywhere in your environment 👍