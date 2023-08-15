### To list share fields like anonymous user
````bash
smbclient -L 127.0.0.1 -N
````

``-L`` --> To list files
`-N` --> Try to connect with anonymous user
`-c` --> to use a command that execute inside the SMB or SAMBA server

---

## Connect to the host

````bash
smbclient //127.0.0.1/myshare -N
````

### Commands inside the connection
dir --> To list the shared files
put --> To upload the indicate file. E.x --> **put output.txt** (they search in the actually directory in local machine) 
`get` --> To download the file indicate. E.x --> get output.txt
`cd` --> move inside the indicate directory.


---
# Tags

---

#tools #smbclient #activedirectory 