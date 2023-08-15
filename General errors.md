# Python

### Error with version python (python2 --> python3 programs)
If you have ever had a problem with a python script like this:
![](../../../Images/Pasted%20image%2020230622205030.png)
This is normally **by the version**, this program is create to execute with python2, and although execute with python2 doesn't work.
The solution is download **2to3** to convert script in python2 to python3 and execute the next command:
> 2to3 --write 46635.py

# GitHub
### Clone subversion repository
When you want to clone a folder that is inside of a project or a folder inside another folder, you obtain an error.

The URL is something like that:
````
https://github.com/vulhub/vulhub/tree/master/kibana/CVE-2018-17246
````

If you use git clone you obtain an error, that say "don't find the repository"
You can use this command:
````bash
svn checkout https://github.com/vulhub/vulhub/trunk/kibana/CVE-2018-17246
````
Is important to **replace the tree/master with trunk**

# Docker

-----

## Docker-compose
If you use docker-compose and launch two or more container but only launch **one**, the error can be the memory map areas a process can have. (This has happened to me with elastic and another container)
The solution is here:
https://github.com/laradock/laradock/issues/1699
You have to execute this command
> sudo sysctl -w vm.max_map_count=262144

==Attention!==
This command is temporally if you want that this want persistent you have to put this line:
- `vm.max_map_count=262144`
In someone of this two files:
`/etc/sysctl.conf`
`/etc/sysctl.d/`

### Inside container

#### Apt update don't work

When we can't do an apt update we can use this command to change the repos of sources

````bash
echo "deb http://archive.debian.org/debian/ stretch main deb http://archive.debian.org/debian-security stretch/updates main " > /etc/apt/sources.list
````

------

# The domain of ctf machine is searched in google

You can use this trick:

1. In your explorer put this:

> about:config

2. Add this 

> browser.fixup.domainsuffixwhitelist.ctf

In the last word put the last part, like ctf, htb...

3. Add with the plus and the state of boolean have to be in true

----


# Tags

#python #github #docker #docker-compose #tools #apache2

-----