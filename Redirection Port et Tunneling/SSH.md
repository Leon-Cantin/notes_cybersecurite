Peut servir à créer un tunnel ou port forward. Très commun.
Lancer un serveur
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo systemctl start ssh
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Voir les connections
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
ss -ntplu
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Supprimer une connection

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
ps -aux |grep ssh
kill <PID>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


## TTY
Doit avoir TTY sur le terminal client pour accepter la connexion

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
python3 -c 'import pty; pty.spawn("/bin/bash")'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


## Proxychains
Transforme le traffic en SOCKS. Il s'accroche à libc (librairie dynamique seulement)
éditer ```/etc/proxychains4.conf``` en ajoutant ```socks5 192.168.50.63 9999``` ou socks4, IP et port du client

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
proxychains [commande]
proxychains nmap -vvv -sT -p4800-4900  -Pn 172.16.188.217
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

proxychain a un timeout très long chiant pour nmap. réduire **cp_read_time_out** et **tcp_connect_time_out**

# Exposer un port
Sur le client, connectez-vous au serveur de cette manière. 127.0.0.1 est du point de vue du serveur.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#Permet d'utiliser le port 8000 sur notre serveur
ssh -L 0.0.0.0:4455:127.0.0.1:8000 anita@192.168.249.246 -p 2222  -i web01/anita_id_ecdsa
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Locale
Lancé depuis un client, envoit des paquets à un serveur ssh qui lui les retransmet
Créer lien
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
ssh -N -L 0.0.0.0:4455:172.16.199.217:445 database_admin@10.4.199.215
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
* -v verbose
* -L pour tunnel locale

10.4.50.215 est entre les 2 et 172.16.50.217 est la destination des paquets (ici SMB)
Le tunnel est entre le client et 172

![[Pasted image 20240118093800.png]]

# Dynamic
comme locale mais permet de retransmettre à plusieurs
le client doit être compatible SOCKS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
ssh -N -D 0.0.0.0:9999 database_admin@10.4.50.215
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Utiliser **Proxychains** pour transformer le traffic en SOCKS.
![[Pasted image 20240118093903.png]]

# Distant
Pour passer un parre-feu
* Serveur
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo systemctl start ssh
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
* client
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
ssh -N -R 127.0.0.1:2345:10.4.188.215:4444 corsaire@192.168.45.196
#	  serveur   	  PGDATABASE01		serveur
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Utilisation sur serveur
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
psql -h 127.0.0.1 -p 2345 -U postgres
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
![[Pasted image 20240118094043.png]]

# Distant dynamique
Mix de dynamique et distant
* Serveur
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo systemctl start ssh
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
* Client
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
ssh -N -R 9998 corsaire@192.168.45.196
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Utiliser **Proxychains** pour transformer le traffic en SOCKS.
Utilisation: (Utiliser IP et port Interne au réseau)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
./ssh_remote_dynamic_client -i 10.4.188.64 -p 9062
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
![[Pasted image 20240118094134.png]]

# SShuttle
Permet de rediriger sur plusieurs sous-réseaux.
Requière root sur le client et python 3 sur le serveur

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#Créer un port forward
socat TCP-LISTEN:2222,fork TCP:10.4.50.215:22
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#Connection SSH avec plusieurs sous-réseaux
sshuttle -r database_admin@192.168.50.63:2222 10.4.50.0/24 172.16.50.0/24
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
192.168.50.63:2222 fait un lien vers 10.4.50.215:22 qui retransmet vers d'autres sous-réseaux
