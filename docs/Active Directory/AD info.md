#### Domain name

```
# User domain
$env:USERDNSDOMAIN
(Get-ADDomain).DNSRoot
```
```
# Computer domain
(Get-WmiObject Win32_ComputerSystem).Domain
```

#### Domain SID

```
# Module AD
Get-ADDomain | select DNSRoot,NetBIOSName,DomainSID
# PowerView
Get-DomainSID
```

#### Forests
Users of a domain in the forest can also access to the other domains.
```
Get-ADForest
```
Functional Modes
```
(Get-ADForest).ForestMode
(Get-ADDomain).DomainMode
```

#### Trusts

The trust direction is the opposite to the access direction.  Incoming trusts allow users of your domain to access the other domain.
If Domain A trusts Domain B, then Domain B can access the ressources of Domain B.

```
nltest /domain_trusts
([System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()).GetAllTrustRelationships()

// PowerView
Get-DomainTrust -API
```
Primary Domain attribute indicates the current domain.

Trusts accounts (used to store the trust keys which are NT hash or Kerberos keys)
```
Get-ADUser  -LDAPFilter "(SamAccountName=*$)" | select SamAccountName
```

#### Users accounts

```
Get-ADUser User
```
```
net user /domain
```
Each computer of the domain has its own user stored as computer class (subclass of user class).

```
# Retrieve all users of the domain including computers and trusts
Get-ADObject -LDAPFilter "objectClass=User" -Properties SamAccountName | select SamAccountName
```

#### Groups

```
Get-ADGroup -Filter * | select SamAccountName
```
```
Get-ADGroup "Domain Admins" -Properties members,memberof
```

#### Domain Controllers

Database location
```
C:\Windows\NTDS\ntds.dit
```

Discovery
```
nslookup -q=srv _ldap._tcp.dc._msdcs.contoso.local
```
```
nltest /dclist:contoso.local
```

#### Computers discovery

```
ldapsearch -H ldap://192.168.100.2 -x -LLL -W -D "anakin@contoso.local" -b "dc=contoso,dc=local" "(objectclass=computer)" "DNSHostName" "OperatingSystem`
```
```
nbtscan 192.168.100.0/24
```
```
ntlm-info smb 192.168.100.0/24
```
nmap
```
nmap --script nbstat -p137 192.168.100.0/24
nmap -p135 192.168.100.0/24
nmap -p139 192.168.100.0/24
```

#### BloodHound

Collectors :
- https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors

```
. .\SharpHound.ps1
Invoke-bloodhound -CollectionMethod All
```
```
neo4j console
./BloodHound --no-sandbox
```
