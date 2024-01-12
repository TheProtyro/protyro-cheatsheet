Don't forget to brute force the services.

#### TCP 21 - FTP

```
wget -r ftp://anonymous:password@$IP/
```
```
# for Active mode
ftp -A $IP 
```

Quick bf : https://github.com/danielmiessler/SecLists/blob/master/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt

```
while read line; do echo $line |cut -d ':' -f 1 ; done < /usr/share/seclists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt > users.txt && while read line; do echo $line |cut -d ':' -f 2 ; done < /usr/share/seclists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt > passwords.txt

hydra -L users.txt -P passwords.txt -e nsr -t 16 ftp://$IP
```

#### TCP 22 - SSH

User enumeration OpenSSH < 7.7 : https://github.com/epi052/cve-2018-15473/blob/master/ssh-username-enum.py

```
./ssh-username-enum.py $IP -w /usr/share/metasploit-framework/data/wordlists/unix_users.txt
```

```
ssh -v user@$IP #check host key algorithm
```

Quick bf : https://github.com/danielmiessler/SecLists/blob/master/Passwords/Default-Credentials/ssh-betterdefaultpasslist.txt

```
while read line; do echo $line |cut -d ':' -f 1 ; done < /usr/share/seclists/Passwords/Default-Credentials/ssh-betterdefaultpasslist.txt > users.txt && while read line; do echo $line |cut -d ':' -f 2 ; done < /usr/share/seclists/Passwords/Default-Credentials/ssh-betterdefaultpasslist.txt > passwords.txt

hydra -L users.txt -P passwords.txt -t 16 ssh://$IP
```

#### TCP 25 - SMTP

```
smtp-user-enum -M VRFY -U /usr/share/seclists/Usernames/Names/names.txt -t 10.1.1.95
```

#### TCP 79 - Finger

```
finger @<Victim>       # List users logged on
finger admin@<Victim>  # Get info of user
finger user@<Victim>   # Get info of user
```
- https://raw.githubusercontent.com/TheProtyro/SimpleScripts/main/FingerUserEnum.py

```
python3 FingerUserEnum.py $IP /usr/share/seclists/Usernames/Names/names.txt --threads 50
```
MSF
```
use auxiliary/scanner/finger/finger_users
```

#### TCP 110 - POP3

- Use telnet to connect, not netcat

```
telnet $IP 110
CAPA    # to obtain the capabilities of the POP3 server.
```

- https://www.shellhacks.com/retrieve-email-pop3-server-command-line/


#### TCP 111 - RPCBIND

```
nmap -sSUC -p111 $IP -oA scans/rpc
```


#### UDP 161 - SNMP
```
hydra -P /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings.txt snmp://$IP
```
```
snmp-check $IP -c public
```
```
snmpwalk -v 2c -c community_string $IP 1.3.6.1 | tee dump.snmp
grep -E ' STRING:' dump.snmp
```
MSF
```
auxiliary/scanner/snmp/snmp_enum
``` 

#### TCP 389,636,3268,3269 - LDAP

```
nmap -n -sV --script "ldap* and not brute" $IP -oA ldapScripts
grep -i 'sam\|pass\|desc' ldapScripts.nmap
```
```
ldapsearch -x -H ldap://$IP -s base namingcontexts
ldapsearch -x -H ldap://$IP -b "DC=<1_SUBDOMAIN>,DC=<TLD>"
ldapsearch -x -H ldap://$IP -D '' -w '' -b "DC=<1_SUBDOMAIN>,DC=<TLD>"
ldapsearch -x -H ldap://$IP -D '<DOMAIN>\<username>' -w '<password>' -b "DC=<1_SUBDOMAIN>,DC=<TLD>"
```

```
ldapsearch -x -H ldap://$IP -D 'DOMAIN\username' -w 'password' -b "DC=domain,DC=local" "(sAMAccountName=user)" description
```

#### TCP 443 - HTTPS

```
/opt/testssl.sh https://$IP/
```

- https://gist.githubusercontent.com/eelsivart/10174134/raw/8aea10b2f0f6842ccff97ee921a836cf05cd7530/heartbleed.py

#### TCP 445 - SMB

```
crackmapexec smb $IP
smbclient -L //$IP/
```
```
smbmap -H $IP -R # Get permissions
```
```
mount -t cifs -o "username=user,password=password" //x.x.x.x/share /mnt/share
```
```
nmap --script smb-enum-shares -p 445 $IP
```
```
enum4linux -a $IP | tee scans/enum4linux.out
```
Not safe :
- Remote Code Execution vulnerability in Microsoft SMBv1 servers (MS17-010)
- Microsoft Windows system vulnerable to remote code execution (MS08-067)
```
nmap --script "smb-vuln-*" -p 139,445 $IP -oA scans/smb_vuln
```
Test for SambaCry, RCE from a writable share in versions >= 3.5 :

```
nmap --script smb-vuln-cve-2017-7494 --script-args smb-vuln-cve-2017-7494.check-version -p445 $IP
```
Brute force using CME
```
crackmapexec smb $IP -u users.txt -p pass.txt --continue-on-success
```
Connect using RPC :
```
rpcclient -U "" -N $IP
rpcclient $> enumdomusers
rpcclient $> querydispinfo
rpcclient $> enumdomgroups
rpcclient $> querygroup 0xRID
rpcclient $> querygroupmem 0xRID
rpcclient $> queryuser 0xRID
rpcclient $> enumprinters
```

#### TCP 1433 - MSSQL

```
nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 <IP>
```
```
while read line; do echo $line |cut -d ':' -f 1 ; done < /usr/share/seclists/Passwords/Default-Credentials/mysql-betterdefaultpasslist.txt > users.txt && while read line; do echo $line |cut -d ':' -f 2 ; done < /usr/share/seclists/Passwords/Default-Credentials/mysql-betterdefaultpasslist.txt > passwords.txt

hydra -L users.txt -P passwords.txt -t 16 mysql://$IP
```

- https://book.hacktricks.xyz/generic-methodologies-and-resources/brute-force#sql-server

```
mssqlclient.py user:passwd@$IP
SQL > help
```

- https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/MSSQL%20Injection.md#mssql-command-execution

#### TCP 2049 - NFS

```
showmount -e $IP
```

```
mkdir mnt_folder

sudo mount -o nolock -o vers=2 $IP:/home mnt_folder/       
# /home directory is being shared and we can access it by mounting it. 
# -o nolock to disable file locking, which is often needed for older NFS servers. 
# -o vers=2  because it doesn't have any authentication or authorization.

cd mnt_folder/ && ls
```

- Look the file permissions (uuid owner and gowner). We can try to add a local user, change its UUID to the one of the remote file, su to that user and try accessing the file or put files (e.g. ssh public key if home folder).

```
sudo adduser pwn
sudo sed -i -e 's/<uid pwn user>/<uid file perm>/g' /etc/passwd      # change the uuid of the pwn user by the one that have permissions on the shared folder

su pwn
id      # notice the uid that allow to access the shared folder
```
#### TCP 3306 - MYSQL

```
nmap -sV -p 3306 --script mysql-audit,mysql-databases,mysql-dump-hashes,mysql-empty-password,mysql-enum,mysql-info,mysql-query,mysql-users,mysql-variables,mysql-vuln-cve2012-2122 $IP
```

#### TCP 3389 - RDP

```
nmap -Pn -sV --script=rdp-vuln-ms12-020 -p 3389 $IP -oA scans/rdp_vuln
```


#### TCP 5800,5801,5900,5901 - VNC

```
nmap -sV --script vnc-info,realvnc-auth-bypass,vnc-title -p 5800,5900 $IP
```

- https://book.hacktricks.xyz/generic-methodologies-and-resources/brute-force#vnc

VNC passwd
```
echo -n <VNC Secret Key> | xxd -r -p | openssl enc -des-cbc --nopad --nosalt -K e84ad660c4721ae0 -iv 0000000000000000 -d | hexdump -Cv
```

Connect to VNC
```
vncviewer [-passwd pass.txt] $IP::5900
```

#### TCP 5432,5433 - POSTGRESQL

```
psql -U <myuser> # Open psql console with user
psql -h <host> -U <username> -d <database> # Remote connection
psql -h <host> -p <port> -U <username> -W <password> <database> # Remote connection
```


#### TCP 8009 - Apache JServ Protocol (AJP)

CVE-2020-1938 : https://www.exploit-db.com/exploits/48143

```
nmap -sV --script ajp-auth,ajp-headers,ajp-methods,ajp-request -n -p 8009 $IP
```
