# Using file-descriptors

````bash
#!/bin/bash

function ctrl_c(){
    echo -e "\n\n [!] Saliendo... [!]"
    tput cnorm # to recovery the cursor in the terminal
    exit 1
}

# Ctrl+C
trap ctrl_c SIGINT

declare -a ports=( $(seq 1 65535) )

ip=$1

checkPort(){
    port=$1

    (exec 3<> /dev/tcp/$ip/$port) 2>/dev/null

    if [[ $? -eq 0 ]]; then
        echo "[+] $port (OPEN) [+]"
    fi

    exec 3<&-
    exec 3>&-
}

tput civis # hide cursor

if [[ $ip ]]; then
    for port in ${ports[@]}; do
        checkPort $port &
    done
else
    echo -e "\n[!] Uso: $0 <ip-address>\n"
fi

wait # Wait that the child/background process finish to continue with the program
# It is typical to see when use & (send a task to the background)

````

# Scanning all the network and the most used ports 
hostDiscovery
````bash
#!/bin/bash

function ctrl_c(){
    echo "-e \n\n[!] Saliendo [!]"
}

tput civis

# Ctrl_c
trap ctrl_c SIGINT

for i in $(seq 1 254); do
    for port in 21 22 23 25 80 139 443 445 8080; do
    timeout 1 bash -c "echo '' > /dev/tcp/192.168.1.$i/$port" 2>/dev/null && echo "[+] Host 192.168.1.$i - PORT $port (OPEN)" &
    done
done

wait

tput cnorm
````
