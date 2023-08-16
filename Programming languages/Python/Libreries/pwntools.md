
To install the librery

> pip3 install pwntools

---

To import the library in our script

> from pwn import *

# Check if a port is available on a host

````python
remote('172.21.7.214',80)
````

# How to check if a range of ports is available

This scan go very slowly, I don't recommend this

````python
for i in range(20,25):
	try:
		remote('172.21.7.214',i)
		print(f"El puerto {i} está abierto!!")
	except:
		print(f"Conexión por el puerto {i} fallida")
````

---

# Tags

