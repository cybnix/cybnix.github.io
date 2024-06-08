# SQL Injection

[How to configure bWAPP with Bee box for your own use](/Guides/EH-Tools-and-Utilities/bwapp)

In this post, I will present some SQL Injection vulnerabilities in bWAPP. SQL injection is a type of cybersecurity attack that exploits vulnerabilities in web applications by inserting malicious SQL code into input fields, allowing attackers to read or manipulate the application's database

## SQL Injection (GET/Search)

So this is the first one in the list. This vulnerability occurs when a web application uses the GET method to retrieve data. With GET requests, the data is sent through the URL, so we can see and modify it directly.

First we will do a search. The easiest thing to try is searching for a single quote [']

<img src="/static/sqli/sql1.png" style="border-radius: 10px; width: 70%;"  />

Seems that the single quote is not properly handled by the web application so it breaks the query and leads to syntax error.

We can try finding the number of columns in the query so we can use UNION queries at a later stage to extract some data. But first we will need the exact number of columns so we can construct such a query that matches the original one.

To do that we can use something like :
```sql 
' ORDER BY 5 -- -
``` 
We can continue increasing the number until we get an error that states something like "unknown column".

<img src="/static/sqli/sql2.png" style="border-radius: 10px; width: 70%;"  />

In our case we got an error when we ordered by 8, so this means that there are 7 columns and we need to craft a query based on that information.

As we got 7 columns, our query can look something like this, Keep in mind that the numbers are only placeholders, which can be replaced:
```sql 
' and 1=0 UNION SELECT 1,2,3,4,5,6,7 -- -
``` 
<img src="/static/sqli/sql3.png" style="border-radius: 10px; width: 70%;"  />

As you can see some of the fields have been populated. 

Okay but what if we change our second placeholder with table_name? We can just write the query the following way :

```sql 
' and 1=0 UNION SELECT 1,table_name,3,4,5,6,7 FROM information_schema.tables -- -
``` 

***information_schema.tables*** basically contains metadata about the tables in the database

So when we run that, we can see some tables being populated in the result field. Usually the users table can be very interesting so we might want to take a deeper look of what is inside:

<img src="/static/sqli/sql4.png" style="border-radius: 10px; width: 70%;"  />

We can do so by adjusting the query like this:

```sql 
' and 1=0 UNION SELECT 1,column_name,3,4,5,6,7 FROM information_schema.columns WHERE table_name='users' -- -
``` 

Same as before ***information_schema.columns*** basically contains metadata about the columns in the table

<img src="/static/sqli/sql5.png" style="border-radius: 10px; width: 70%;"  />

So we got some interesting columns from the users table.

Now let's try to extract the information for some users. We will get the email, login and password from the users table with the following query:

```sql 
' and 1=0 UNION SELECT 1,email,login,password,5,6,7 FROM users -- -
``` 
<img src="/static/sqli/sql6.png" style="border-radius: 10px; width: 70%;"  />

And finally we got the users and their passwords. So the password for both has the same hash so we can try to retrieve it.

So this seems to be a SHA-1 hash.

<img src="/static/sqli/sql7.png" style="border-radius: 10px; width: 70%;"  />

After running hashcat towards this hash which I've saved in a file, it seems that the password was cracked in seconds.

<img src="/static/sqli/sql8.png" style="border-radius: 10px; width: 70%;"  />

<br><br>
I have used the following command with hashcat to crack the password. I might do another post in the future to show how hashcat works and how you can use it with different wordlists🔢.
``` bash 
hashcat -a 0 -m 100 hash /usr/share/wordlists/rockyou.txt
``` 

So this was only one of the many examples that we can show regarding SQL Injections in bWAPP. I will present more in the future in the same post. One really cool method is to use sqlmap and automate the process which is very usable and it was shared to me by a professional that most of the times during real life engagements, they are using automated methods for that instead of manually testing it.


---

