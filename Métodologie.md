Prendre des notes. 
* un dossier par machine
* un fichier de creds
* Tout ce qu'on fait
Ne pas sauter trop vite d'une machine à l'autre
Vérifier toutes nos options

# Énumération
Utiliser nmap
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo nmap -sC -sV mailsrv1/nmap 192.168.50.242
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Rechercher les applications que l'on ne connait pas.
Rechercher pour exploits. **Rechercher plus loins dans google**
Ne pas arrêter si on trouve un accès, continuer d'énumérer
Regarder les différents services disponibles
Si on trouve rien, essayer UDP 161 et 162 (SNMP)
Garder en tête la vue d'ensemble

# WEB
nikto
essayer divers listes de divers fournisseurs (seclist, dirb, dirbuster...)

# Lecture de fichiers
Lire les utilisateurs
* /etc/passwd
* clef SSH /home/[user]/.ssh/id_rsa

# Escalade de privilèges
Pour aller vite, utiliser winPeas ou linPeas
* GTFObins
* Regarder les droits de l'usager
	* sudo -l, sudoer
	* whoami /all
	* droits sur les fichiers
		*  permissions
		* setuid, capabilities
* Processus
	* pspy, ps -aux
	* cron
* Services/tâches
* Fichiers qui traînent
* /etc/shadow 		pour les mots de passes
	
# Partages
```
crackmapexec smb 192.168.50.234 -u john -p password123 --shares
````

# Situational Awareness
Vérifier les interfaces réseaux et trouver autres machines
Tous les utilisateurs
Droits sur le réseau des utilisateurs (RDP, Admin, shares ... )

Vérifier les ports ouvert localement