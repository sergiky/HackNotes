File Transfer Protocol
Port 21 by default

## Bruteforce password with hydra
````bash
hydra -l sergio -P passwords.txt ftp:127.0.0.1 -t 15
````

if **-l or -p** is lowercase you say that you know what is the username, if you put the param in capital you have to **indicate a dictionary**

``-t`` --> Indicate **threads**

If you found a program, you need to use the mode binary to download, if not can be that the data may become corrupted