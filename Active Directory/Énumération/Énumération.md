Prendre en comptes tous les comptes bas niveau, ils peuvent souvent avoir des droits différents selon leur rôle.
## Externe
Il est possible d'énumérer certaines choses depuis l'externe sans accès.
#### Kerbrute
la pré-authentification de Kerberos ne génère habituellement pas d'alerte. Peut-être utilisé pour savoir si un usagé existe.
```shell-session
kerbrute userenum -d INLANEFREIGHT.LOCAL --dc 172.16.5.5 jsmith.txt -o valid_ad_users
```

#### SMB Null Session
```shell
enum4linux -U 172.16.5.5  | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]"

rpcclient -U "" -N 172.16.5.5
	enumdomusers
	
netexec smb 172.16.5.5 --users
```
#### LDAP anonymous bind
N'est plus utilisé par défaut depuis Windows Serveur 2003, mais parfois actif.
```shell
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "(&(objectclass=user))"  | grep sAMAccountName: | cut -f2 -d" "
```
```shell
./windapsearch.py --dc-ip 172.16.5.5 -u "" -U
```

## Politique de mot de passe
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~powershell
net accounts
get-addomain | get-adobject -properties * | select *pwd*
Get-ADDefaultDomainPasswordPolicy
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
### SMB
```shell-session
netexec smb 172.16.5.5 -u avazquez -p Password123 --pass-pol
```
```shell-session
rpcclient -U "" -N 172.16.5.5
querydominfo
getdompwinfo
```
### avec LDAP
```shell-session
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
```
## Usagers
``` powershell
net user /domain
net user <nom> /domain
#powerview
Get-DomainUser -Domain LOGISTICS.INLANEFREIGHT.LOCAL | select SamAccountName
```

#### SMB
```shell
netexec smb 172.16.7.3 --users -u AB920 -p weasal | awk '{ print $5 }' | cut -d '\' -f2 > users.txt
```

#### LDAP
*LDAP anonymous bind s'applique aussi*

#### DomainPasswordSpray
Peut générer une liste d'usagers si machine relié au domaine.
```powershell
Import-Module .\DomainPasswordSpray.ps1
Get-DomainUserList -Domain domainname -RemoveDisabled -RemovePotentialLockouts | Out-File -Encoding ascii userlist.txt
```

## Groupes
```
net group /domain
net group <nom> /domain
```
### SMB
```
netexec smb 172.16.5.5 --groups
```

## Permissions et utilisateurs authentifiés
### Powerview
```Find-LocalAdminAccess``` Cherche où notre compte est admin. utilise la fonction OpenServiceW qui se connecte au Service Control Manager sur chaque machine avec la permissions SC_MANAGER_ALL_ACCESS. Si la connection réussi, nous devrions être admin

```Get-NetSession``` utilise NetWkstaUserEnum et NetSessionEnum. le premier demande admin et le deuxième demande accès la clef de registre SrvsvcSessionInfo

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Get-netsession -computername web04 -verbose
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

ne fonctionne plus depuis windows 10 16299 (1709) et windows server 2019 1809

### PsLoggedon
Cherche qui est authentifié sur quelle machine
Nécessite Remote Registry service

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.\PsLoggedon.exe \\files04
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

## Sessions en cours
```powershell-session
qwinsta
```

### SMB
```shell-session
netexec smb 172.16.5.130 -u forend -p Klmcargo2 --loggedon-users
```

## Comptes de services (SPN)
LocalSystem, LocalService, NetworkService
services sont intégré à l'AD avec un Service Principal Name (SPN). Associe un service avec un compte de service.
Format: ```<service class>/<host>:<port>/<service name>```
service name est optionnel.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
setspn -L iis_service
#powerview
Get-NetUser -SPN | select samaccountname,serviceprincipalname
Get-DomainUser -SPN -Domain FREIGHTLOGISTICS.LOCAL | select SamAccountName
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```
setspn -T medtech.local -F -Q */*
```


## Permissions d'objet (ACL)
peut avoir un ensemble de permissions  avec plusieurs Access Control Entries (ACE)
Vérification se fait par l'envois d'un jeton d'accès qui est ensuite valider sur la liste de ACE.
1. Chercher sur quoi nous pouvons agir (ACL dessus)
2. Suivre les liens entre les groupes

### PowerView
Sort beaucoup d'information
```powershell-session
Find-InterestingDomainAcl
```

#### Version plus ciblé (résolution automatique des GUID)
```powershell
Import-Module .\PowerView.ps1
$sid = Convert-NameToSid wley
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid} 
```
#### (Résolution manuel des GUID)
```powershell
Get-DomainObjectACL -Identity * | ? {$_.SecurityIdentifier -eq $sid}
#convertir le GUID (ObjectAceType) en version lisible.
$guid= "00299570-246d-11d0-a768-00aa006e0529"
Get-ADObject -SearchBase "CN=Extended-Rights,$((Get-ADRootDSE).ConfigurationNamingContext)" -Filter {ObjectClass -like 'ControlAccessRight'} -Properties * |Select Name,DisplayName,DistinguishedName,rightsGuid| ?{$_.rightsGuid -eq $guid} | fl
```
#### Informations sur les groupes parents d'un groupe
```powershell
Get-DomainGroup -Identity "Help Desk Level 1" | select memberof
```

### Natif
```powershell
Get-ADUser -Filter * | Select-Object -ExpandProperty SamAccountName > ad_users.txt
# script extrayant tout lien avec notre principale "wley"
foreach($line in [System.IO.File]::ReadLines("C:\Users\htb-student\Desktop\ad_users.txt")) {get-acl  "AD:\$(Get-ADUser $line)" | Select-Object Path -ExpandProperty Access | Where-Object {$_.IdentityReference -match 'INLANEFREIGHT\\wley'}}

```

## DNS
Avec un utilisateur il est possible de lister les noeuds DNS puis de faire ??
```shell
adidnsdump -u inlanefreight\\forend ldap://172.16.5.5
```
-r peut trouver plus?

## Mauvaise configuration

### Mot de passe dans description
```powershell
Get-DomainUser * | Select-Object samaccountname,description |Where-Object {$_.Description -ne $null}
```

### PASSWD_NOTREQD
```powershell
#powerview
Get-DomainUser -UACFilter PASSWD_NOTREQD | Select-Object samaccountname,useraccountcontrol
```
### mot de passe réversible
```powershell
#powerview
Get-DomainUser -Identity * | ? {$_.useraccountcontrol -like '*ENCRYPTED_TEXT_PWD_ALLOWED*'} |select samaccountname,useraccountcontrol
```

## Partage de domaine
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
#powerview
Find-DomainShare
#utiliser -CheckShareAccess pour voir ce qu'on a accès seulement
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
**SYSVOL** se trouve à ```%SystemRoot%\SYSVOL\Sysvol\domain-name```
Contient des politiques et scripts qui pourraient être intéressant.
utiliser ls et cat pour naviguer et lire

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
ls \\dc1\sysvol\corp.com\
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```shell-session
netexec smb 172.16.5.5 -u forend -p Klmcargo2 --shares
netexec smb 172.16.5.5 -u forend -p Klmcargo2 -M spider_plus --shares 'Department Shares'
```
```shell-session
smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H 172.16.5.5
smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H 172.16.5.5 -R 'Department Shares' --dir-only
```

### Snaffler
Cherche sur les partages de l'AD pour trouver des creds. Doit être sur un hôte joint de l'AD ou un usager de l'AD.
```bash
Snaffler.exe -s -d inlanefreight.local -o snaffler.log -v data
```

## Permissions d'exécution
### PowerView
```Powershell
Get-NetLocalGroupMember -ComputerName ACADEMY-EA-MS01 -GroupName "Remote Desktop Users"
#Powershell remoting/WinRM
Get-NetLocalGroupMember -ComputerName ACADEMY-EA-MS01 -GroupName "Remote Management Users"
```
### Bloodhound
```cypher
#powershell remoting/WinRM
MATCH p1=shortestPath((u1:User)-[r1:MemberOf*1..]->(g1:Group)) MATCH p2=(u1)-[:CanPSRemote*1..]->(c:Computer) RETURN p2
#SQL
MATCH p1=shortestPath((u1:User)-[r1:MemberOf*1..]->(g1:Group)) MATCH p2=(u1)-[:SQLAdmin*1..]->(c:Computer) RETURN p2
```

## Trusts
```powershell
Import-Module activedirectory
Get-ADTrust -Filter *
#Powerview
Get-DomainTrust
Get-DomainTrustMapping
#netdom
netdom query /domain:inlanefreight.local trust
```
Usagers étrangers à un domaine
```powershell
#powerview
Get-DomainForeignGroupMember -Domain FREIGHTLOGISTICS.LOCAL
Convert-SidToName S-1-5-21-3842939050-3880317879-2865463114-500
```

# Autre outils
* PingCastle
* AdExplorer (sysinternalsSuite)
* Group3r
	* Cherche dans les politiques et sysvol
* ADRecon