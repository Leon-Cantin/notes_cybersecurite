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
## Énumération
* Pile de technologies
	* BD
	* Serveur
	* CDN
	* Languages
* Sous-domaines
* pages
### Passive
* Wappalyzer
* En-têtes (x-powered-by)
### Active
* Fuzz (essayer divers listes de divers fournisseurs (seclist, dirb, dirbuster...)):
	* sous domaines
	* pages/fichiers
	* Charactères spéciaux et autres chaînes douteuses
* automatique (nikto, wpscan):
* SSRF: balayer les ports internes ou machines internes.

# Lecture de fichiers
Lire les utilisateurs
* /etc/passwd
* Liste de fichiers commun lors d'un LFI
* clef SSH /home/[user]/.ssh/id_rsa
* Lire les fichiers communs du CDN

# Escalade de privilèges
Pour aller vite, utiliser winPeas ou linPeas
* GTFObins
* Regarder les droits de l'usager
	* sudo -l, sudoer
	* whoami /all
	* droits sur les fichiers
		* permissions
			* Windows: Utiliser automatismes comme module msfconsole `services_permission`
		* SUID, GUID, capabilities
* Processus
	* `pspy`, `ps auxww`
	* `cron`
* Services/tâches/localhost
* Fichiers de config
* Tester réutilisation de mots de passes

# Situational Awareness
Vérifier les interfaces réseaux et trouver autres machines
Tous les utilisateurs
Droits sur le réseau des utilisateurs (RDP, Admin, shares ... )

Vérifier les ports ouvert localement