#### Windows

```
netsh wlan show profile
netsh wlan show profile <SSID> key=clear
```

#### Linux

```
sudo grep -r '^psk=' /etc/NetworkManager/system-connections/
```
