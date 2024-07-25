![[Pasted image 20240118094311.png]]

# Chisel
Formatte le traffic en HTTP. Peut requérir une version différente sur le client. Télécharger sur leur github.
Le serveur écoute localement sur le port 1080

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
chisel server --port 8080 --reverse
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

--reverse: quand un client se connecte, un port SOCKS est créé sur le serveur
Sur le client:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
chisel client 192.168.118.4:8080 R:socks > /dev/null 2>&1 &
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Ensuite on discute par SOCKS

**Attention: ce n'est pas toutes les versions qui sont compatible avec le client.**

ssh ne peut pas se connecter sur un SOCKS générique.
Utiliser ncat et ProxyCommand de ssh pour se connecter en ssh.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
ssh -o ProxyCommand='ncat --proxy-type socks5 --proxy 127.0.0.1:1080 %h %p' database_admin@10.4.50.215
#%h sera remplacé par l'hôte et %p par le port.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

## Forward de port local
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#server
chisel server -p 4567 --reverse
#client
chisel client 192.168.45.239:4567 R:8090:127.0.0.1:8000
#accéder ensuite par 127.0.0.1:8090
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
