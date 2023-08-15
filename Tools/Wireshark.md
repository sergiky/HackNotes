# Filters

#### Search the packet that have ip destination indicated
````bash
ip.dst == 192.168.1.1
````

#### Search fragmented packets
````bash
ip.flags.mf == 1
````

#### Search no-fragmented packets
````bash
ip.flags.mf == 0
````
### Search the packets in a determinated port
````bash
tcp.port == 22
````

