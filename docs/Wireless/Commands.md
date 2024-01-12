#### Monitor mode

```
airmon-ng check kill
airmon-ng start wlan0

airmon-ng stop wlan0
```

```
sudo ip link set wlan0 down
sudo iwconfig wlan0 mode monitor
sudo ip link set wlan0 up
```

Verify:
```
sudo iw dev wlan0mon info
sudo iwconfig wlan0mon  #deprecated
```

#### Injection test

```
sudo aireplay-ng -9 -e wifu -a <BSSID> wlan0mon
```
Card2Card:
```
sudo aireplay-ng -9 -i wlan1mon wlan0mon
```

#### View or set channel

```
sudo ip link set wlan1 down
sudo iwconfig wlan1 channel 12
sudo ip link set wlan1 up
iwlist wlan1 channel
```

- https://stackoverflow.com/questions/29560308/how-to-force-wifi-adapter-to-use-specific-channel

1. look for your interface
```
sudo iwconfig
```

2. look at your channels
```
sudo iwlist {interface} channel
```

3. turn off wifi
```
sudo iwconfig {interface} power off
```

4. set your desired channel
```
sudo iwconfig {interface} channel {channel, ex. "23"}
```

5. set your desired frequency
```
sudo iwconfig {interface} freq {frequency, ex. "5.00G"}
```

6. turn on wifi
```
sudo iwconfig {interface} power on
```

