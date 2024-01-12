#### Prerequisites

Monitor mode
```
airmon-ng	
airmon-ng check
airmon-ng check kill
airmon-ng start wlan1
airmon-ng start wlan0 3 # Start on channel 3
iw dev wlan1 info
```

#### Recon

```
airodump-ng wlan1 --band abg --wps -w recon
```

```
wash -i wlan
wash -i wlan -5
```

#### Filtering

use the -b option and set the access point MAC address to keep. This will filter out all additional APs from the **unencrypted** capture file.

```
sudo airdecap-ng -b <BSSID> capture.cap
```

#### Graphs

Clients to AP Relationship Graph (CAPR) = won't draw an AP that doesn’t have any clients

```
airgraph-ng -o graph.png -i capture.csv -g CAPR
```

Clients Probe Graph (CPG) = displays the relationships between wireless clients and probed networks

```
airgraph-ng -o graph.png -i capture.csv -g CPG
```