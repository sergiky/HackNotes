To convert a number into binary we can use this:
````bash
echo "$(echo "obase=2; 192" | bc)"
````

If you want tu convert this ip 192.168.1.42 into binary:
````bash
echo "$(echo "obase=2; 192" | bc).$(echo "obase=2; 168" | bc).$(echo "obase=2; 1" | bc).$(echo "obase=2; 42" | bc)"
````

