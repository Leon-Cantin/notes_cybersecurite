# Security identifier (SID)
Valeur unique assigné à divers entités comme les usagers ou les groupes. Localement généré par Local Security Authority(LSA). Sur les usagers et groupes de domaines par Domain Controller (DC).

# Niveau d'intégrité
* System: SYSTEM (kernel, ...)
* High: Elevated users
- Medium: Standard users
- Low: very restricted rights often used in sandboxed[^privesc_win_sandbox] processes or for directories storing temporary data

# Users Access Control (UAC)
Protège contre l'exécution d'applications. Quand un utilisateur s'authentifi, il reçoi 2 jetons. Un utilisateur standard et un admin régulier pour exécuter opérations privilégiés. 

# Comptes pré-existant
## Utilisateur:
- Administrator
## Groupes:
- Administrators			
- Backup Operators: Accès aux fichiers pour sauvegardes
- Remote Desktop Users: RDP
- Remote Management Users: WinRM

# Information à obtenir
* Nom d'utilisateur et d'hôte
	* whoami
* groupes de l'utilisateur courant et privilèges
	* whoami /all
* utilisateurs et groupes existant
	* Get-LocalUser
	* Get-LocalGroup
	* Get-LocalGroupMember \<group>
	* net user \<utilisateur>
	* net localgroup
* système d'exploitation, version et architecture
	* systeminfo
* Information réseau
	* ipconfig /all  - Interfaces réseau
	* route print
	* netstat -ano   -  Connections ouvertes
* applications installées
	* Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
	* Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
	* Vérifier les dossiers d'installations (Program Files, download) pour non-installations
* processus actifs
	* Get-Process

# Recherche de fichiers

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
gci -Path C:\Users -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue
#Cherche les fichiers simples
gci -Path C:\Users -Include * -File -Recurse -ErrorAction SilentlyContinue
#cherche les fichiers cachées et exclue desktop.ini
gci -Path C:\Users -Include * -File -Recurse -ErrorAction SilentlyContinue -Attributes Hidden -exclude desktop.ini,container.dat,NTUSER.DAT*,ActivationStore.dat*,settings.dat*
#cherche fichiers clé ssh
gci -Path C:\Users -Include .ssh,.vnc -Recurse -ErrorAction SilentlyContinue -Attributes Hidden

gci -Path .\ -Recurse -exclude *.exe,*.dll | Select-String -Pattern "pass" | select filename,linenumber,line
gci -Recurse -Path N:\ | Select-String "cred" -List

findstr /spin "password"
#find all those password and credential strings in config files.
dir /s pass == cred == vnc == .config
#HtB
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
* L'outil de recherche de la barre de tâche Windows
* Outils automatisés
	* [Lazagne](https://github.com/AlessandroZ/LaZagne) `start lazagne.exe all`

# Powershell
Historique sauvegardé auto. **Get-History** pour l'accéder. **Clear-History** pour le nettoyer
Module PSReadLine journalise aussi **(Get-PSReadlineOption).HistorySavePath** pour trouver le chemin d'accès.
**Event Viewer** peut logger des commandes powershell

# Automatiser
Faire attention, ne trouve pas tout.
	winpeas: **/usr/share/peass/winpeas/winPEASx64.exe**
	seatbelt: https://github.com/r3motecontrol/Ghostpack-CompiledBinaries
	JAWS

# runas

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
runas /user:backupadmin cmd
runas /netonly /user:domain\user command
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Activer RDP

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#désactiver restriction admin
reg add "HKLM\System\CurrentControlSet\Control\Lsa" /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0;
netsh advfirewall firewall set rule group="remote desktop" new enable=Yes;
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# IIS
Si admin on peut récupérer les mots de passe du service web

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
C:\Windows\System32\inetsrv>appcmd list apppools
C:\Windows\System32\inetsrv>appcmd list apppools /text:name
C:\Windows\System32\inetsrv>appcmd list apppool "MyTestPool" /text:*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
