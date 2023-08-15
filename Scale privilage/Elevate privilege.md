
-----

# Automated Tools

- [[lse]]
- [[linenum]]
- [[pspy]] 

----

# Manual recon

- See the groups that belongs the user, e.x: if user is in docker group you can escalate privileges with mounts 
- With **sudo -l** to see if we have some permission at sudoers (this only work if don't request a password)
- Search for privilege SUID `find / -perm -4000 -ls 2>/dev/null`
- See the capabilities with `getcap -r / 2>/dev/null` (some or there are sensitive like cap_setuid)
- crontab -l --> To see if the user have some task executing in the system at regular time intervals
- cat /etc/crontab --> If a user of the system is executing a script in X time
- systemctl list-timers --> How many times left to execute some task

### Create an script to see the command running in the system

> procmon.sh

````bash
#!/bin/bash

ctrl_c(){
    echo -e "\n\n[*] Saliendo... [*]\n"
    tput cnorm
    exit 1
}

# ctrl_c
trap ctrl_c SIGINT

tput civis

old_process=$(ps -eo user,command)

while true; do
    new_process=$(ps -eo user,command)
    diff <(echo "$old_process") <(echo "$new_process") | grep "[\>\<]" | grep -vE "command|kworker|procmon"
    old_process=$new_process
done
````

`ps -eo user,command` --> Show the user that execute the command and the command

`diff <(echo "$old_process") <(echo "$new_process")` --> The difference between the content of variables. The new lines that don't exists in old file (this case variable) is represented with a **>** and the lines that only stay in file1 (old_process) with **<**

`grep "[\>\<]"` --> Grep by this or this. New things or old things

`grep -vE "command|kworker|procmon"` --> Remove from output the lines that contain this words

--- 

# Tags

#elevatePrivilages

-----