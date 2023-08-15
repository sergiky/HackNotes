# What is netcat and for what is used?
[Netcat](https://en.wikipedia.org/wiki/Netcat) is a tool used in Windows and GNU/Linux OS to listening ports, transfer data, scanning port, create [backdoors](https://en.wikipedia.org/wiki/Backdoor_(computing))...

----------

# Installation in Debian

> sudo apt install netcat

-----------

# Other type of version

ncat is the predecessor of netcat, it is less powerful but something you have to used
This is very similar to nc
> sudo apt install ncat

----------

## Help Panel
Remember **you have the man of nc** that give more information
![](../../../Images/Pasted%20image%2020230627195748.png|800)

-------

### Explain some parameters
`-n` --> Don't apply DNS resolution, like -n in nmap
`-l` --> Listen mode, for inbound connection
`-v` --> verbose to give more information
`-p` --> To indicate the port to use

---------

# Tags

#netcat #tools

---------