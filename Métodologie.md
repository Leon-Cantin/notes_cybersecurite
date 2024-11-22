Prendre son temps et vérifier toutes nos options

# Énumération
Utiliser nmap
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo nmap -sC -sV mailsrv1/nmap 192.168.50.242
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Rechercher les applications que l'on ne connait pas.
Rechercher pour exploits. **Rechercher plus loins dans google**
Ne pas arrêter si on trouve un accès, continuer d'énumérer
Si on trouve rien, essayer UDP 161 et 162 (SNMP)
Garder en tête la vue d'ensemble

# WEB
## fuzz 
* sous domaines
* fichiers
nikto
essayer divers listes de divers fournisseurs (seclist, dirb, dirbuster...)
* SSRF: balayer les ports internes ou machines internes.
* listes de mots pour characteres spéciaux

# Lecture de fichiers
Lire les utilisateurs
* /etc/passwd
* Liste de fichiers commun lors d'un LFI
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
* Services/tâches/localhost
* Fichiers de config
* Tester réutilisation de mots de passes
	
# Partages
```
crackmapexec smb 192.168.50.234 -u john -p password123 --shares
````

# Situational Awareness
Vérifier les interfaces réseaux et trouver autres machines
Tous les utilisateurs
Droits sur le réseau des utilisateurs (RDP, Admin, shares ... )

Vérifier les ports ouvert localement