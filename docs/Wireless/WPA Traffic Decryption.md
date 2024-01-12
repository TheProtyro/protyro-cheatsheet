- The 4-way handshake must be in the capture (required to generate PTK for the targeted client; PTK is used to encrypt data and is unique for each client). Having (packets 2 and 3) or (packets 3 and 4) will work correctly, at least with airdecap-ng.

With Wireshark:

- Specify a key (Passphrase PSK or PMK) via: Edit > Preferences > Select IEEE 802.11 from the Protocol list, check "Enable Decryption". Click "Edit" button next to "Decryption Keys"

To generate the wpa-psk:
```
wpa_passphrase TheSSID ThePassword
```

https://wiki.wireshark.org/HowToDecrypt802.11

With airdecap-ng:

```
airdecap-ng -e 'TheSSID' -p 'ThePassword' capture.cap
```
