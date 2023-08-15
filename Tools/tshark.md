Like [[Wireshark]] but in terminal

````bash
tshark -r captura-cap -Y "http" 2>/dev/null
````

You can transform the information in json
````bash
tshark -r captura-cap -Y "http" -Tjson 2>/dev/null
````

If you want to see only a field you can use:
````bash
tshark -r captura-cap -Y "http" -Tfields -e tcp.payload 2>/dev/null
````

Remember you can use grep...