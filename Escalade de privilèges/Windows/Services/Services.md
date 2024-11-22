Corrélation des services, droits et chemins est difficle nativement. Utiliser des outils.

# Lister
services.msn, Get-Service cmdlet ou Get-CimInstance cmdlet (Les 2 derniers ne fonctionnent pas en WinRM ou bind shell, utilisez RDP)
Pour récupérer les services actifs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Get-Service Spooler
services.msn
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Un chemin d'accès non standard peut signifier des privilèges déffectueux par une instalation utilisateur
Obtenir liste droits sur un service:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
icacls "C:\xampp\apache\bin\httpd.exe"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Info sur le service
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Get-CimInstance -ClassName win32_service | Select * | Where-Object {$_.Name -match 'RemoteServerWin'}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Lancer ou arrêter un service
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
net stop [service]
net start [service]
start-service [service]
stop-service [service]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Redémarrer. Besoin de SeShutdownPrivilege ou SeRemoteShutdownPrivilege
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
shutdown /r /t 0
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# PowerUp
Détecte des points d'escaledes **/usr/share/windows-resources/powersploit/Privesc/PowerUp.ps1**
Doit exécuter powershell avec **ExecutionPolicy Bypass** 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
powershell -ep bypass
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Import-Module .\PowerUp.ps1
 . .\PowerUp.ps1
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
**Get-ModifiableServiceFile** liste services modifiables
Peut remplacer un service qui créé usager: **john** avec passe: **Password123!** comme admin