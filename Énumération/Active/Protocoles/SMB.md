permet de partager des fichiers ou services et imprimantes sur un réseau.
Version originale et SMB utilise NetBIOS ports 137, 138, 139
CIFS dialecte plus moderne et utilise port 445 seulement.
Samba version UNIX qui implémente CIFS
Les accès sont par partage et incluent execute, read et full access

# Outils
## NMap
certains scripts “ls /usr/share/nmap/scripts/smb*”
Vérifier port 445 et 139

## enum4linux-ng
Énumère un paquet d'info sur un server SMB. 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
enum4linux-ng 192.168.50.120 -A
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
-A pour une bonne énumération

## Net view (Windows)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
net view \\dc01 /all
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

## smbclient

```shell 
smbclient //192.168.201.10/offsec
```

-N pour session anonyme (null)
-L pour lister

## rpcclient
RPC est un concept et s'applique à un serveur SMB

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#se connecter avec un utilisateur SMB ou null
rpcclient -U "" 10.129.14.128
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Peut exécuter diverses commandes intéressantes 

| Query 		|		Description |
| --- | --- |
| srvinfo |			Server information. |
| enumdomains |	Enumerate all domains that are deployed in the network. |
| querydominfo |	Provides domain, server, and user information of deployed domains. |
| netshareenumall |	Enumerates all available shares. |
| netsharegetinfo <share> |	Provides information about a specific share. |
| enumdomusers |	Enumerates all domain users. | 
| queryuser <RID> |	Provides information about a specific user. |

## impacket-samrdump
alternative à rpcclient
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
impacket-samrdump 10.129.14.128
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

## smbmap	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
smbmap -H 10.129.14.128
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
