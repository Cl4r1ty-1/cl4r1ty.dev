---
layout: writeup
title: alphaascii clashing
tags: crypto
---

We are given a docker instance to connect to and a zip file to download

![alt text](https://github.com/Cl4r1ty-1/CTF/blob/main/HTB%20University/images/Pasted%20image%2020241216142504.png?raw=true)

Inside the zip file is a `server.py` file that we can assume is also being run on the docker container. The server seems to be a simple login app, with the credentials to users stored in a dictionary and checked by first hashing the username and password using md5 and comparing that against user input.

We see this interesting `for else` loop for the login functionality

{% highlight python %}
for db_user, v in users.items():
	if [usr_hash, pwd] == v:
		if usr == db_user:
			print(f'[+] welcome, {usr} 🤖!')
                else:
                        print(f"[+] what?! this was unexpected. shutting down the system :: {open('flag.txt').read()} 👽")
                        exit()
                break
else:
	print('[-] invalid username and/or password!')
{% endhighlight %}

We want to get the "this was unexpected" line of code to run to print the flag. Digging a little deeper into the way md5 works in this program we see that it checks whether the md5 of the user's credentials in the dictionary matches with the md5 of the user's input and if this is false it will execute the unexpected line of code. If we can find a way get past the `for else` loop we may be able to make this happen.

We come across and being begin researching md5 `clashes` which are 2 different pieces of data that produce the same md5 hash. The data inputted in to this program when registering an account must only consist of ascii characters and numbers so we need to find 2 alphascii strings that produce an md5 clash, hence the name of the challenge.

{% highlight python %}
if usr.isalnum() and pwd.isalnum():
	usr_hash = md5(usr.encode()).hexdigest()
	if usr not in users.keys():
		users[usr] = [md5(usr.encode()).hexdigest(), pwd]
        else:
                print('[-] this user already exists!')
else:
	print('[-] your credentials must contain only ascii letters and digits.')

{% endhighlight %}

After a bit of googling we find this X (Twitter) post: https://x.com/realhashbreaker/status/1770161965006008570
In which this lovely gentleman decided to find an alphascii md5 clash for fun...as you do.

By connecting to the server and registering with one of these values as both the username and password, then logging in with the with the other value as the username and the first value as the password, we were able to get the flag

```
clarity@Ben-PC:~$ nc -v 94.237.61.84 49146
Connection to 94.237.61.84 49146 port [tcp/*] succeeded!

    Welcome to my login application scaredy cat ! I am using MD5 to save the passwords in the database.
                          I am more than certain that this is secure.
                                   You can't prove me wrong!

    [1] Login
    [2] Register
    [3] Exit

    Option (json format) :: {"option":"register"}
enter credentials (json format) :: {"username":"TEXTCOLLBYfGiJUETHQ4hAcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak", "password":"TEXTCOLLBYfGiJUETHQ4hAcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak"}

    Welcome to my login application scaredy cat ! I am using MD5 to save the passwords in the database.
                          I am more than certain that this is secure.
                                   You can't prove me wrong!

    [1] Login
    [2] Register
    [3] Exit

    Option (json format) :: {"option":"login"}
enter credentials (json format) :: {"username":"TEXTCOLLBYfGiJUETHQ4hEcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak", "password":"TEXTCOLLBYfGiJUETHQ4hAcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak"}
[+] what?! this was unexpected. shutting down the system :: HTB{finding_alphanumeric_md5_collisions_for_fun_https://x.com/realhashbreaker/status/1770161965006008570_dc24d380cdcb6ddcfc16ce9043f10e84} 👽
```

Flag:
`HTB{finding_alphanumeric_md5_collisions_for_fun_https://x.com/realhashbreaker/status/1770161965006008570_dc24d380cdcb6ddcfc16ce9043f10e84}`
