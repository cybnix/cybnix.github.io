# Crunch - wordlist generator

---

## What is Crunch?

<img src="/static/crunch.svg" style="border-radius: 10px; width: 20%;"/>

In this short post I will present the tool "crunch". What this tool does is generating a dictionary file based on your input. Yes, there are probably thousands of wordlists online but you can craft your own wordlist specifically tailored for your needs. In the [Cracking WPA/WPA2](/Simulated-Attacks/Network-and-Wireless-Attacks/crackingwpa) post, we have used one wordlist that came with Kali but in other cases we might need something more specific. This is where tools like Crunch come in handy.

!!!warning 
Please remember, never use those tools for malicious purposes, especially without having permission to do so.
!!!

---

## How to get the tool?

You can check the tool here : [!button size="s" corners="pill" variant="contrast" text="crunch"](https://www.kali.org/tools/crunch/) 

However if you have a Kali Linux already, don't worry as it comes pre-installed. 

---

## How to use crunch?

<img src="/static/crunch1.png" style="border-radius: 10px"/>
<br></br>

When we type **crunch** in our terminal, we can see that it prompts us to add a "min" , "max" and some other options. Min and max are basically the minimum and maximum lenght of the strings that would be generated.

Let's try something. We will use **"crunch 1 4 1234 -o wordlist1.txt"**. What this will do is that it will generate a combination of all numbers and will use length from 1 to 4. and it will output it to a file called "wordlist1.txt"
<br></br>
<img src="/static/crunch2.png" style="border-radius: 10px"/>
<br></br>
<img src="/static/crunch3.png" style="border-radius: 10px"/>
<br></br>
Now as you can see, it generated 340 lines with different combinations with the numbers 1-4.

### Using patterns to generate a wordlist

So here comes the concern why you shouldn't use anything that makes sense for your password. Avoid using your names, birthdate, phone number or any personal information as your password. Don't even combine them to create a password! 

Let's say your name is John Doe and you were born on 10th of January 1990. If someone knows this information about you, they can use it to generate a wordlist based on that.

We can try it out like that for example :

**crunch  -o john_doe.txt -p John Doe 10 January 1990**

> - *-p flag means that we will define a pattern for generating a wordlist.*
> - *-o flag is used to define the name of the output file*
> - **When using -p I haven't specified the min and max length because it won't affect the output since -p generates all possible permutations of the given words. Basically crunch will generate every possible way to arrange the provided words by us.**

<img src="/static/crunch4.png" style="border-radius: 10px"/>
120 strings generated with this information

---

And that's not all, that's only us touching the surface of crunch with this small example. This tool is way more powerful and it has many more options. The point here is that you should avoid using stuff that make sense for your password as it could potentially lead to you being a victim of a targeted attack.

!!!
If you want to learn more about crunch, I would strongly recommend you to check the crunch manual. You can do so with "man crunch" in your terminal and do a few experiments of generating a wordlist for yourself. 
!!!