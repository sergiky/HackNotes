
SMB is the original protocol and samba is used in to share files with GNU/Linux systems (samba use smb)


### Mounts
If there are a lot of directory and files you can play with mounts

Tools to facility the transfer of files in Windows  
````bash
sudo apt install cifs-utils
````

Create the folder where you want to mount the smb
````bash
sudo mkdir /mnt/mounted
````

````bash
sudo mount -t cifs //127.0.0.1/myshare /mnt/mounted
````

`-t cifs` --> Indicate the type that is going to be cifs

Normally request a password, but the Anonymous user is available.
If you don't that request a password you can put this parameters:

````bash
sudo mount -t cifs //127.0.0.1/myshare /mnt/mounted -o username=null,password=null,domain=,rw
````
Now you can use tree to see a better structure

==Important!!==
If you change some folder or something in this mount folder, you **change in the original machine**

You can use the command **umount /mount/mounted** and you don't see files and folder...

