# Bash
You have an script in bash to convert your terminal in a interactive console/
````bash
script /dev/null -c bash
````

**Ctr-Z**

````bash
stty raw -echo;fg
reset xterm
````

````bash
export TERM=xterm
export SHELL=bash
````

```bash
stty rows 38 columns 116
```

---

# rlwrap

You can use rlwrap that is a tool that emulate an "interactive shell" 

````bash
sudo rlwrap nc -nlvp PORT
````

---

# Tags

#interactive-console #tty #bash #rlwarp

---