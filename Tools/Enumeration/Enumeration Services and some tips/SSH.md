Typical connection
````bash
ssh sergio@127.0.0.1 -p 2222
````

## BruteForce
### Hydra
````bash
hydra -l sergio -P /usr/share/wordlists/rockyou.txt ssh://127.0.0.1 -s 2
222 -t 4
````

``-s`` --> Port
``-t`` --> Threads