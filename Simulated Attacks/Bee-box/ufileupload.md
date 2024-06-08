# Unrestricted File Upload (medium security level)

[How to configure bWAPP with Bee box for your own use](/Guides/EH-Tools-and-Utilities/bwapp)

## Getting started

In this post I will present a way to exploit the unrestricted file upload vulnerability in bWAPP(bee box). I will be using medium security level so I can show you how you can bypass it. If the security is low, basically there are no blocked extensions so you can directly upload a webshell. In our case there are restrictions. You can see on the below screenshot that we are unable to upload our **php-backdoor.php** file.


<img src="/static/bwapp/unrestricted_file_upload_m/upload1.png" style="border-radius: 10px; width: 80%;"  />

Of course! Why would they allow you to upload a php file in a place where images are required, right?

Well there are some extensions blocked, but not all of them are 😏.

---

## Step 1 - Uploading an allowed file

I will upload a .png file. However the trick here is, that I will intercept the request with Burp and this is where the magic will happen.

<img src="/static/bwapp/unrestricted_file_upload_m/upload2.png" style="border-radius: 10px; width: 60%;"  />
<br><br>

---

## Step 2 - Intercepting the request with Burp
Before I press Upload, my Burp proxy will be running as well as my Burp.


So here is my request before and after being edited.
<br><br>
<img src="/static/bwapp/unrestricted_file_upload_m/upload3.png" style="border-radius: 10px; width: 60%;"  />
<br><br>
<img src="/static/bwapp/unrestricted_file_upload_m/upload4.png" style="border-radius: 10px; width: 80%;"  />

What did I change?

Well as you can see we have changed the extension of the file first. We have added .phtml in the end of the file. This extension typically will indicate a file containing PHP code but embedded within HTML. Some web servers may recongize that as a PHP file and process it, just as in our case.

The next thing I did was putting the PHP code along with the image data. This code is a very simple command injection payload. It would allow us to execute system commands.

Now when we forward the request, the server will accept it as there is no .php extension at the end of the file.
<img src="/static/bwapp/unrestricted_file_upload_m/upload5.png" style="border-radius: 10px; width: 80%;"  />

---

## Step 3 - Navigating to our malicious file and executing commands

Now when we click on the file, we are led to this page showing us an error that it cannot execute a blank command.
<img src="/static/bwapp/unrestricted_file_upload_m/upload6.png" style="border-radius: 10px; width: 80%;"  />

Well, we can just append **?cmd=** at the end of our url and write our system command. By adding this to the URL, this would allows us to pass a command to our web shell which we have embedded in the uploaded file, which then will be executed on the server.
<br><br>
<img src="/static/bwapp/unrestricted_file_upload_m/upload7.png" style="border-radius: 10px; width: 80%;"  />

Here's another command that allows us to list the files in the current directory. We can even see some .php shells that I have uploaded previously while I was playing around. They could pass as the security was put to low.

<img src="/static/bwapp/unrestricted_file_upload_m/upload8.png" style="border-radius: 10px; width: 80%;"  />
<br><br>

And a "pwd" one so we can see the current working directory.

<img src="/static/bwapp/unrestricted_file_upload_m/upload9.png" style="border-radius: 10px; width: 80%;"  />
<br><br>

---

## Conclusion

In conclusion, I can say that unrestricted file upload in web applications could be very dangerous. Developers should really make sure that the files are being checked properly before upload. If such vulnerability is present, attackers can gain control over the server and potentially steal sensitive data. 

This was a quite enjoyable exercise to do in Bee box so I am planning on doing more in the future. Stay tuned if you are interested 👀.