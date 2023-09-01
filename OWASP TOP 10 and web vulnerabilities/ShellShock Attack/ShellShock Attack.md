
# What is an ShellShock attack

It's a vulnerability recognized in GNU/Linux system that allow to an attacker to execute commands from a remote mode, know as **Bashdoor** 

This vulnerability was discover in 2014

---

# When can exploit this?

- When the version of the GNU Bash will be 1.13-**4.3** version
- Normally if you find a **/cgi-bin/** is recommendable to enum a shell shock attack

---


# Exploiting

A practice example of exploiting this is [SickOS 1.1](../../CTF/VulnHub/SickOS%201.1.md) machine from VulnHub

In cgi-bin folder, you can enum files with some of this extensions: ls,sh,cgi, you can use [Gobuster](../../Tools/Enumeration/Gobuster.md)

To exploit this attack we are going to play with the **User-Agent** 

In this case we can use this oneline, from the [cloudflare blog website](https://blog.cloudflare.com/inside-shellshock/):

````bash
curl -H "User-Agent: () { :; }; /bin/eject" http://
````

With /bin/eject you are able to open the floppy drive


----

# Labs

You can use [SickOS 1.1](../../CTF/VulnHub/SickOS%201.1.md) machine from Vulnhub (don't realize by an error to discover the machine)

---

# Tags

#vulnerabilty #shellshock