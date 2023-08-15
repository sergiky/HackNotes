
# What is iptables

Is a tool used to configure inbound and outbound rules for connections and packages (like [firewall](https://en.wikipedia.org/wiki/Firewall_(computing)))

-----

# How to install

> sudo apt install iptables

-----

# Examples
## Accept all the connection in port 80
```bash
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

## Accept all the connection in port 80 in eth0 interface
````bash
iptables -A OUTPUT -p tcp -m tcp -o eth0 --sport 80 -j ACCEPT
````

### Block all the connections in all ports

````bash
iptables -A INPUT -p tcp --dport 0:65535 -m conntrack --ctstate NEW -j DROP
````

## Block all the connections in eth0

```bash
iptables -A OUTPUT -p tcp -m tcp -o eth0 --sport 80 -j ACCEPT
```

--------

# Some parameters

`--flush or -F` --> Delete all the rules and disable then, but when reboot the computer the configured rules will active.
`-A` --> **Append**. To permit connection INPUT/OUTPUT...
`-p` --> Indicate the **protocol** tcp, udp...
``-dport`` -->Indicate the **destination port** 80,443...
`-j` --> **Jump**.  Established the action to be taken if the traffic match with the rule. ACCEPT/REJECT
`-m conntrack` --> Use the module of follow connections. It is useful to take decisions according to the state of the network-
`--ctstate NEW` --> The rules apply to packet that have a state NEW.
`-o` --> **out-interface**. Indicate the interface

-----

# Tags

#tools #iptables

-------