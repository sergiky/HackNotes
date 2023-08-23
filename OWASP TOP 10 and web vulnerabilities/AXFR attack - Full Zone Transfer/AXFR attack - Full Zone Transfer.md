
Domain Zone Transfer Attack

This attack is used to obtain names of domains, and the name of the server that are associates with the domain or domains.

We are abusing the protocol [DNS](https://en.wikipedia.org/wiki/Domain_Name_System) 

In this case we are going to do a copy of the database and IP

``AXFR`` --> Is the zone transfer complete 

``IXFR`` --> Zone transfer incremental, we take the data from the last transfer until now, this are more rare to found

This type of attack is rare to see, at the day, the real server are well configured

---

# Exploiting

![](../../Images/Pasted%20image%2020230823162229.png)

In this file (that is the database)

We can see the name of the servers that have the protocol DNS

 > ns1, ns2
 
 And the names in bottom left are the subdomain

## Enum name servers

````bash
dig ns @127.0.0.1 sergiky.local
````

## Enum mail services

````bash
dig mx @127.0.0.1 sergiky.local
````

# AXFR ATTACK

````bash
dig axfr @127.0.0.1 sergiky.local
````

---

# Labs

- https://github.com/vulhub/vulhub/tree/master/dns/dns-zone-transfer


---

# Tags

#vulnerabilty #axfr 